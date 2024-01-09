# Ubuntu-OpenVAS
This is a description of how I installed OpenVAS on a Ubuntu Server on an Orange PI 5B.  
I was using this to take notes on how the build went as I had several other attempts that did not work.
Mostly they were due to the versions of Ubuntu that I was using (the one was the "official"
Orange PI Ubuntu.  I questioned the security of it when it didn't require a password on boot.  
It also doesn't "feel" like a normal Ubuntu so I'm not sure what they did to it.

The second was a deprecated version from Joshua-Riek.  I did not realize that the
repositories were no longer up and working.

I used the Ubuntu server image from https://github.com/Joshua-Riek

I heavily relied on input from Greenbone's build documentation to install OpenVAS.
It is located here:
https://greenbone.github.io/docs/latest/22.4/source-build/index.html#building-22-4-from-source
I did make some changes as this was specifically for Ubuntu, so references to the other systems are not here.
I also added in some server set up notes that I did to minimally secure and get some other capabilities to work.

There are no guarantees that any of this will work for you so this is all "as is".
If you see any glaring mistakes or changes please let me know.

