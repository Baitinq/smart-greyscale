#!/bin/bash

# Requires redshift and picom

period=300
location=""

trap cleanup EXIT

cleanup()
{
    killall picom 2> /dev/null
}

check_if_picom_already_running()
{
    if pgrep -x "picom" > /dev/null
    then
        echo "PICOM IS ALREADY RUNNING, PLEASE KILL IT AND EXECUTE THE SCRIPT AGAIN."
        exit 1
    fi
}

check_and_enable_if_night_or_day()
{
    if redshift -l $location -p | grep -i -q "day"; then
        killall picom
    else
        if ! pgrep -x "picom" > /dev/null; then
            picom -b --config /dev/null --backend glx --glx-fshader-win  "uniform sampler2D tex; uniform float opacity; void main() { vec4 c = texture2D(tex, gl_TexCoord[0].xy); float y = dot(c.rgb, vec3(0.2126, 0.7152, 0.0722)); gl_FragColor = opacity*vec4(y, y, y, c.a); }" &    
        fi
    fi
}

while getopts ':l:p:' flag; do
    case "${flag}" in
        l) location="${OPTARG}"                  ;;
        p) period="${OPTARG}"                    ;;
    esac
done

main()
{
    if [ "$location" == "" ]; then
        echo "Need location argument [-l (LAT:LON)]"
        exit 1
    fi

    check_if_picom_already_running

    while true 
    do
        check_and_enable_if_night_or_day 

        sleep $period
    done
}

main
