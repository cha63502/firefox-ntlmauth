# Finding the Tools Menu #
The new Firefox UI doesn't display the old File/Edit/Tools menu bar by default. Push the `Alt` key to make it show up. `Alt+T` will open the Tools menu directly.

# Adding Sites #

When adding a site to the list of integrated-authentication-enabled sites, it may at first appear to not be working. This is usually because the site entered isn't actually the site that is asking Firefox for your credentials. Here are some tips to help you get it working:

  * **Don't use wildcards.** Wildcards (`*`) are actually valid characters in a URL. Instead, just put the site. For example, `http://mysite` rather than `http://mysite/*`.
  * **Remove the trailing slash.** It may be that the site asking for your credentials is identifying itself without a trailing slash - try removing it. For example, `http://mysite` rather than `http://mysite/`.
  * **Consider omitting the protocol.** If you have a site where you use both http and https, you can just specify the site name. For example, `www.mysite.com`.
  * **Consider only listing the domain.** You can specify just a domain like `somedomain.org` rather than listing every site in that domain. This helps in local intranet scenarios.
  * **Consider enabling non-FQDN pass-through**. If you're just trying to enable pass-through for sites on your local domain, click the checkbox at the bottom for enabling pass-through on all non-FQDN sites. That way, if you only specify the hostname (`sharepoint`) without the full domain (`sharepoint.mylocaldomain.com`) you'll get pass-through auth without having to list every single local site.
  * **Look at what the authorization box asks for.** Try logging into the site before adding it to the list of NTLM-enabled sites. The box that pops up ("Authentication Required") and asks for your username and password should have a line at the top that says "Enter username and password for <site URL>" and show you the URL to the site asking for credentials. That is what you should put in the list of NTLM-enabled sites.

![http://firefox-ntlmauth.googlecode.com/svn/site/wiki-images/authentication-dialog.png](http://firefox-ntlmauth.googlecode.com/svn/site/wiki-images/authentication-dialog.png)

# Enabling Pass-Through for All Local Sites #
If you are on a local network (home, work) with a lot of sites where you might have them addressed by the non-fully-qualified-name (`http://mysite`) or the fully qualified name (`http://mysite.mydomain.local`) then you can get pass-through auth to work by doing these two steps:
  * **Add your domain to the list.** Don't include a protocol or a server name. Using the example above, you'd add `mydomain.local` to the list.
  * **Check the "Enable pass-through on all non-FQDN sites" box.** You'll see this at the bottom of the dialog.

# Behind the Scenes #

When you add a site to the list of integrated-authentication-enabled sites it allows Firefox to start using pass-through authentication on that site. It uses the [integrated authentication configuration keys](https://developer.mozilla.org/en/Integrated_Authentication) already built into Firefox. As such, the URLs entered can't use wildcards or special formatting - they need to be what Firefox expects.

If you need to manually manipulate the setting outside of the add-on, simply type "about:config" in the Firefox address bar and hit Enter. If you're shown a warning, click the button to let you through and view settings. In the "Filter" box that appears at the top of the list of settings, copy and paste this:

`-uris`

You will see the three configuration keys for SPNEGO (integrated) auth:
```
network.negotiate-auth.trusted-uris
network.negotiate-auth.delegation-uris
network.automatic-ntlm-auth.trusted-uris
```

You can then modify the list of authorized sites manually.

The automatic non-FQDN pass-through is handled by two keys introduced in Firefox 13:
```
network.negotiate-auth.allow-non-fqdn
network.automatic-ntlm-auth.allow-non-fqdn
```

**IMPORTANT: This add-on does not actually DO the authentication.** Firefox itself already has built-in NTLM (Windows pass-through) and other integrated authentication, it's just not obvious how to get it to work. This add-on makes it easy to configure Firefox to use its already-existing features, but _it does not do the authentication proper_.

# If You Can't See The Menu... #

If you are having trouble finding the menu from the add-on, here are some troubleshooting steps:

  * Make sure you hit the `Alt` key to see the `Tools` menu, or hit `Alt + T` to open the `Tools` menu directly. You should see "Integrated Authentication Sites" in that menu.
  * If you don't see "Integrated Authentication Sites" in the `Tools` menu, try uninstalling the add-on and then reinstalling it.
  * If you still don't see it...
    * [Create a new Firefox profile](https://support.mozilla.org/en-US/kb/profile-manager-create-and-remove-firefox-profiles) just for testing that has no other add-ons or extensions installed. Install the add-on using that test profile and see if it shows up. If it does, it indicates there may be a problem with your Firefox profile.
    * As a last resort, [try resetting Firefox, which fixes many problems](https://support.mozilla.org/en-US/kb/reset-firefox-fix-most-problems). You will have to reinstall your add-ons, but most other data is kept.

I have had a few issue reports where people can't find the menu, but **I can't reproduce the issue**. I had one person who reported it solve the problem and **it was a problem with their local Firefox profile**.

If you can't see the menu and you've followed all the troubleshooting steps, [feel free to file an issue](https://code.google.com/p/firefox-ntlmauth/issues/list) but **please include steps to reproduce, details on your OS version, Firefox version, etc.** Again, I've as yet been unable to reproduce it, so these steps and additional information is really important.