# Raspberry Pi

## DuckDNS

### What is it about
- Configure DuckDNS to check your public IP each 5 minutes

### Instructions
1. Create a hidden directory with

   ```shell
   mkdir ~/.duckdns
   ```

2. Create a shell script file with:

   ```shell
   nano ~/.duckdns/duck.sh
   ```
   
   And enter the line provided by [www.duckdns.org](https://www.duckdns.org/install.jsp), similar to:

   ```shell
   echo url="https://www.duckdns.org/update?domains=user&token=xxxxxxxxxxxxxxxxxxx&ip=" | curl -k -o ~/duckdns/duck.log -K -
   ```
   
3. Change permissions with:

   ```shell
   sudo chmod 700 ~/.duckdns/duck.sh
   ```
   
4. Edit the **crontab** with:

   ```shell
   crontab -e
   ```
   
   and append:
   
   ```shell
   */5 * * * * ~/.duckdns/duck.sh >/dev/null 2>&1
   ```
   
5. Launch DuckDNS now with:

   ```shell
   ./duck.sh
   ```
   
6. Check it works with

   ```shell
   cat duck.log
   ```
   
7. Make DuckDNS check your IP each 5 minutes with:

   ```shell
   sudo service cron start
   ```