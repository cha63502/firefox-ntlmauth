# Introduction #

This is a pretty simple add-on, but I'm kinda new to Firefox add-ons so it's good to have a plan. Now that it's out there, some additional things I'd like to get in.

**IMPORTANT NOTE:** The add-on itself **does not actually DO the NTLM authentication.** It simply makes it easier to configure the functionality that already exists in Firefox. As such, there are no plans to get NTLM auth working for other operating systems or to get the plugin working in other browsers... _unless Firefox (or the other plugin-capable browsers) add that support_, then, maybe. :)

# Integration with Auth Dialog #

I want to integrate with the authentication dialog so when you access an NTLM-enabled site and the username/password dialog pops up, an additional button will appear that says "Use NTLM Auth" or something.

This is going to require figuring out [how to write XPCOM in JavaScript](https://developer.mozilla.org/en/How_to_Build_an_XPCOM_Component_in_Javascript) because I need to have a window event watcher that catches the dialog popping up and also checks the corresponding request to see if it's NTLM-aware.