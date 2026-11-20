win10扫描占用xx端口的程序并强制关闭 

- netstat -ano | findstr :${port} 
- tasklist | findstr ${port}
-  taskkill /PID ${pid} /F 

刷新 DNS

- ipconfig /flushdns

