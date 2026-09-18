# Description of the IT architecture


## Komodo

Timm:

> Changes to the application code end up in the image, so once your CI has published a new release-expanded, Deploy in Komodo pulls it and that works. docker-compose.yml and docker/nginx.conf are different, they are read directly from the checkout on our server and Komodo does not pull that from GitHub. I have updated the checkout and restarted nginx, so #177 is live now. Next I will change the deploy so that it pulls the repo first, then Deploy in Komodo is all you need.


