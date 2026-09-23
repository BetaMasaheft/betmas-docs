# Description of the IT architecture


## Komodo

Timm:

> Changes to the application code end up in the image, so once your CI has published a new release-expanded, Deploy in Komodo pulls it and that works. docker-compose.yml and docker/nginx.conf are different, they are read directly from the checkout on our server and Komodo does not pull that from GitHub. I have updated the checkout and restarted nginx, so #177 is live now. Next I will change the deploy so that it pulls the repo first, then Deploy in Komodo is all you need.

Timm:

> Automatic updates are already in place, no cron needed. Komodo checks every night at 03:00 whether there is a newer image for any service of the stack, and if so deploys it by itself, with the same steps as a manual deploy. The manual Deploy button stays available at any time. So the only thing left is on your side: at the moment the image is built weekly. If Claudius switches build-data.yml to nightly, the test instance updates nightly, as long as the build is finished before 03:00 our time (01:00 UTC).
