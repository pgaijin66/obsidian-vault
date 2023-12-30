
```
#!/bin/bash

readonly ROCKETCHATCTL_DOWNLOAD_URL="https://install.rocket.chat/rocketchatctl"

readonly ROCKETCHATCTL_DIRECTORY="/usr/local/bin"

if [ ${EUID} -ne 0 ]; then

echo "This script must be run as root. Cancelling" >&2

exit 1

fi

if ! [[ -t 0 ]]; then

echo "This script is interactive, please run: bash -c \"\$(curl https://install.rocket.chat)\"" >&2

exit 1

fi

if [ ! -f "$ROCKETCHATCTL_DIRECTORY/rocketchatctl" ]; then

curl -L $ROCKETCHATCTL_DOWNLOAD_URL -o /tmp/rocketchatctl

if [ $? != 0 ]; then

echo "Error downloading rocketchatctl."

exit 1

else

mv /tmp/rocketchatctl $ROCKETCHATCTL_DIRECTORY/

chmod 755 $ROCKETCHATCTL_DIRECTORY/rocketchatctl

fi

$ROCKETCHATCTL_DIRECTORY/rocketchatctl install $@

else

echo "You already have rocketchatctl installed, use rocketchatctl to manage your RocketChat installation."

echo "Run rocketchatctl help for more info."

exit 1

fi
```


```
get_os_distro() {

if [ -r /etc/os-release ]; then

distro="$(. /etc/os-release && echo "$ID")"

distro_version="$(. /etc/os-release && echo "$VERSION_ID")"

distro_codename="$(. /etc/os-release && echo ${VERSION_CODENAME:-})"

fi

}

os_supported() {

get_os_distro

case "$distro" in

ubuntu)

[[ "$distro_version" =~ (("18.04"|"19.04"|"19.10"|"20.04"|"21.04"|"21.10")) ]] || print_distro_not_supported_error_and_exit

;;

debian)

[[ "$distro_version" =~ (("9"|"10"|"11")) ]] || print_distro_not_supported_error_and_exit

;;

centos)

[[ "$distro_version" =~ (("7"|"8")) ]] || print_distro_not_supported_error_and_exit

;;

*)

print_distro_not_supported_error_and_exit

;;

esac

}
```

https://github.com/RocketChat/install.sh/blob/master/rocketchatctl