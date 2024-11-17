#!/bin/bash

# Copyright (c) 2024 by Philip Collier, github.com/AB9IL
# This script is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 3 of the License, or
# (at your option) any later version. There is NO warranty; not even for
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

# This script makes it easy to have a curated list of Bluesky profiles to
# read on several topics.  You need to keep one topic file with a few topics
# and Bluesky, Threads, or Twitter / X handles.
#
# Notes:
# one topic per line
# separate the topic name from the Bluesky hanhdles with a comma, then a comma
# to separate handles for the various platforms. One space between handles.
#
# The syntax for topic file is:
# topic name, blueskyhandle1 blueskyhandle2, threadshandle1, xhandle1 xhandle2
#
# Topic Examples:
# Linux Experts, manjarolinux nixcraft itsfoss2 linux4everyone omgubuntu
# Open Source Intelligence, bellingcat auroraintel michaeldweiss Gerjon_ arictoler
#
#==============================================================================
# User defined variables
# path to topic file
topicpath="$HOME/.config/tw_alltopics"

# base url of Bluesky redirect and Threads
BASES=(https://bsky.app/profile/ https://www.threads.net/@ https://twiiit.com/)

# suffix for Bluesky urls
SUFFIX=".bsky.social"

# Browser command
BROWSER="x-www-browser"

#==============================================================================
# CAUTION: DRAGONS LIVE BELOW THIS LINE
#==============================================================================
getlist(){
# Create a group of user-defined user profiles.
[[ "$OPMODE" == "gui" ]] && PROFILE_LIST="$(rofi -dmenu -p "Enter one to five Bluesky handles")"
[[ "$OPMODE" != "gui" ]] && echo "Enter one to five Bluesky handles" && read PROFILE_LIST

# Exit if no data is entered.
[[ -z "$PROFILE_LIST" ]] && echo "something went wrong" && exit 1

PROFILES=$PROFILE_LIST
}

# define operating mode
OPMODE=$1

# read the bookmarks
readarray OPTIONS < $topicpath

# Add an option to edit the list
OPTIONS+=("Edit Topic List, Edit")

# open a menu in Rofi or fzf
[[ "$OPMODE" == "gui" ]] && COMMAND="rofi -dmenu -p Select -l ${#OPTIONS[@]}"
[[ "$OPMODE" == "gui" ]] || COMMAND="fzf --layout=reverse --header=Select:"

# Select the desired topic
REPLY=$(echo "${OPTIONS[@]}" | sed 's/^ //g;/^$/d;/#.*/d' | awk -F, '{printf $1"\n"}' | $COMMAND )

# Quit if no selection
[[ -z "$REPLY" ]] && echo "No selection made, exiting..." && exit 0

# Match the topic and list of Bluesky profiles
BPROFILES=$(echo "${OPTIONS[@]}" | grep "$REPLY" | sed 's/^ //g;s/, /,/g' | awk -F, '{printf $2"\n"}')

# Match the topic and list of threads profiles
TPROFILES=$(echo "${OPTIONS[@]}" | grep "$REPLY" | sed 's/^ //g;s/, /,/g' | awk -F, '{printf $3"\n"}')

# Match the topic and list of twitter / X profiles
XPROFILES=$(echo "${OPTIONS[@]}" | grep "$REPLY" | sed 's/^ //g;s/, /,/g' | awk -F, '{printf $4"\n"}')

# Quit if unable to get profiles
[[ -z "$BPROFILES$TPROFILES" ]] && echo "Something went wrong, exiting..." && exit 1

[[ "$BPROFILES" == "Edit" ]] && (x-terminal-emulator -e vim $topicpath) && exit


open_tab(){
# Create urls and open browser tabs
URL="--new-tab ${2}${1} "
# use proxychains if connection is in proxied mode
[ -f "/tmp/proxyflag" ] && proxychains4 $BROWSER $URL
[ ! -f "/tmp/proxyflag" ] && $BROWSER $URL
}

# Complete properly formed urls for Bluesky or threads
[[ -z "$BPROFILES" ]] || for HANDLE in ${BPROFILES[@]}; do open_tab "${HANDLE}${SUFFIX}" "${BASES[0]}";done
[[ -z "$TPROFILES" ]] || for HANDLE in ${TPROFILES[@]}; do open_tab "${HANDLE}" "${BASES[1]}";done
[[ -z "$XPROFILES" ]] || for HANDLE in ${XPROFILES[@]}; do open_tab "${HANDLE}" "${BASES[2]}";done
exit
