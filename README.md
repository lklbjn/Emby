<h3>VPS+EMBY+GD=Netflix</h3>

1.安装EMBY

   <pre>wget https://github.com/MediaBrowser/Emby.Releases/releases/download/4.4.2.0/emby-server-deb_4.4.2.0_amd64.deb</pre>
    
   <pre>dpkg -i emby-server-deb_4.4.2.0_amd64.deb</pre>
   
登录xxx.xxx.xxx.xxx:8096

如果无法打开网址，请将8096端口开放

Ubuntu系统可用以下代码开发端口<pre>iptables -F</pre>

2.0安装rclone
   <pre>curl https://rclone.org/install.sh | sudo bash</pre>
2.1 配置rclone

    rclone config
3.0挂载GD

    mkdir -p /home/gdrive  
    /usr/bin/rclone mount emby: /home/gdrive \
     --umask 0000 \
     --default-permissions \
     --allow-non-empty \
     --allow-other \
     --buffer-size 32M \
     --dir-cache-time 12h \
     --vfs-read-chunk-size 64M \
     --vfs-read-chunk-size-limit 1G &
3.1查看挂载

    df -h
    
3.2自动挂载

    cat > /etc/systemd/system/rclone.service <<EOF
    [Unit]
    Description=Rclone
    AssertPathIsDirectory=LocalFolder
    After=network-online.target
    
    [Service]
    Type=simple
    ExecStart=/usr/bin/rclone mount emby: /home/gdrive \
     --umask 0000 \
     --default-permissions \
     --allow-non-empty \
     --allow-other \
     --buffer-size 32M \
     --dir-cache-time 12h \
     --vfs-read-chunk-size 64M \
     --vfs-read-chunk-size-limit 1G
    ExecStop=/bin/fusermount -u LocalFolder
    Restart=on-abort
    User=root
    
    [Install]
    WantedBy=default.target
    EOF
     
4.设置开机自启
    <pre>systemctl start rclone</pre>
    <pre>systemctl enable rclone</pre>
5.安装BBRPLUS加速
<pre>wget -N --no-check-certificate "https://raw.githubusercontent.com/Asgard520/Emby/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh</pre>
6.设置虚拟内存  

    wget https://www.moerats.com/usr/shell/swap.sh && bash swap.sh


   

