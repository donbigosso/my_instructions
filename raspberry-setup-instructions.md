# Raspberry setup Instructions #

## Printer setup ##
If printer is recognized and visible, but does not print (processing then stopping), a proprietary plugin must be installed:
```
sudo apt update
sudo apt install hplip hplip-gui printer-driver-hpijs printer-driver-hpcups
```
afterwards plugin must be downloaded:
```
sudo hp-plugin
```
Follow the recommended path

Checking printer name

```
lpstat -p
```

If printer is found, print like this
```
lp -d HP-LaserJet-Professional-P1102 -P 1-2 dokument.pdf 
```
-P indicates the page range, -n - numer of copies

## Docker ##
Setting up an Ubuntu container to experiment. It will be removed after exiting.

```
docker run -it --rm ubuntu bash
```

``` -it ``` : Connects you interactively to the container's terminal.
``` --rm ``` : Automatically removes the container when you exit, keeping your system clean.
``` ubuntu ``` : Uses the latest official Ubuntu image.

### Checking Docker version ###
```
docker version
docker compose version
```
### Retrieving Docker statistics ###
```
docker stats
```

## Installing SSL certificate ##
(for donbigosso.polafri.pl)
```
docker run --rm \
  -v "$(pwd)/certs/data:/etc/letsencrypt" \
  -v "$(pwd)/certs/www:/var/www/certbot" \
  certbot/certbot certonly --webroot -w /var/www/certbot \
  --email polafri@gmail.com -d donbigosso.polafri.pl --non-interactive --agree-tos
```
The response should be:

```
Unable to find image 'certbot/certbot:latest' locally
latest: Pulling from certbot/certbot
94e9d8af2201: Pull complete 
ada0c44c46b7: Pull complete 
90ed12a8da4e: Pull complete 
9ccb167dacef: Pull complete 
ead82108f605: Pull complete 
9eebdc6aacde: Pull complete 
58556a2bc41c: Pull complete 
e9c28927343c: Pull complete 
4bd42a9b38cb: Pull complete 
9b434edd335c: Pull complete 
2e3534240c06: Pull complete 
Digest: sha256:5255405f241cd64b121f36ef0172711420816bbbd9c029674de53e5ed953182d
Status: Downloaded newer image for certbot/certbot:latest
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Account registered.
Requesting a certificate for donbigosso.polafri.pl

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/donbigosso.polafri.pl/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/donbigosso.polafri.pl/privkey.pem
This certificate expires on 2026-03-29.
These files will be updated when the certificate renews.
NEXT STEPS:
- The certificate will need to be renewed before it expires. Certbot can automatically renew the certificate in the background, but you may need to take steps to enable that functionality. See https://certbot.org/renewal-setup for instructions.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```
### Adding crontab task to renew the certificate ###
```
0 0,12 * * * docker run --rm -v "~/php-ssl-server/certs/data:/etc/letsencrypt" -v "~/php-ssl-server/certs/www:/var/www/certbot" certbot/certbot renew && docker-compose -f ~/php-ssl-server/docker-compose.yml restart nginx
```
This will check twice daily for certificate renewal. Path must be updated.
