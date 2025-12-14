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


