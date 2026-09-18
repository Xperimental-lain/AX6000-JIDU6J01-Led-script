# AX6000-JIDU6J01-Led-script

ssh then do the following 


```sh id="nphwbf"
vi /root/led_internet.sh
```

```sh
#!/bin/sh

# LED names
GREEN_LED="green:status-0"
RED_LED="red:status-0"

# Ping targets
PING1="8.8.8.8"
PING2="1.1.1.1"

LAST_STATE=""

while true; do

    # Check internet connectivity
    if ping -c 1 -W 2 $PING1 >/dev/null 2>&1 || \
       ping -c 1 -W 2 $PING2 >/dev/null 2>&1; then
        STATE="UP"
    else
        STATE="DOWN"
    fi

    # Only change LEDs if state changed
    if [ "$STATE" != "$LAST_STATE" ]; then

        if [ "$STATE" = "UP" ]; then

            # GREEN ON
            echo none > /sys/class/leds/$GREEN_LED/trigger
            echo 1 > /sys/class/leds/$GREEN_LED/brightness

            # RED OFF
            echo none > /sys/class/leds/$RED_LED/trigger
            echo 0 > /sys/class/leds/$RED_LED/brightness

            logger "[LED] Internet UP"

        else

            # RED BLINK
            echo timer > /sys/class/leds/$RED_LED/trigger
            echo 300 > /sys/class/leds/$RED_LED/delay_on
            echo 300 > /sys/class/leds/$RED_LED/delay_off

            # GREEN OFF
            echo none > /sys/class/leds/$GREEN_LED/trigger
            echo 0 > /sys/class/leds/$GREEN_LED/brightness

            logger "[LED] Internet DOWN"

        fi

        LAST_STATE="$STATE"
    fi

    sleep 5
done
```

Save it:

## How to Save and Exit Vi

1. Press the **`Esc`** key (to enter Command Mode).
2. Type **`:wq`**
3. Press **`Enter`**



Make executable:

```sh id="jlwmdd"
chmod +x /root/led_internet.sh
```

Run manually:

```sh id="xrzwyz"
/root/led_internet.sh
```

Run in background:

```sh id="31q27y"
/root/led_internet.sh &
```

Auto-start on boot:

Edit:

```sh id="wzbrc4"
vi /etc/rc.local
```

Add before `exit 0`:

```sh id="zrn9kr"
/root/led_internet.sh &
```

Then reboot and it will start automatically.
