# service files

/usr/lib/systemd/system/spark-worker0.service

```text
Unit]
Description=Apache Spark Master and Slave Servers
After=network.target
After=systemd-user-sessions.service
After=network-online.target

[Service]
User=root
Type=forking
WorkingDirectory=/usr/local/spark
ExecStart=/usr/local/spark/sbin/start-slave.sh spark://10.101.16.102:7077
ExecStop=/usr/local/spark/sbin/stop-slave.sh
TimeoutSec=30
Restart= on-failure
RestartSec= 30
StartLimitInterval=350
StartLimitBurst=10

[Install]
WantedBy=multi-user.target

```

/usr/lib/systemd/system/spark-master.service

```text
[Unit]
Description=Apache Spark Master and Slave Servers
After=network.target
After=systemd-user-sessions.service
After=network-online.target

[Service]
User=root
Type=forking
WorkingDirectory=/usr/local/spark
ExecStart=/usr/local/spark/sbin/start-master.sh
ExecStop=/usr/local/spark/sbin/stop-master.sh
TimeoutSec=30
Restart= on-failure
RestartSec= 30
StartLimitInterval=350
StartLimitBurst=10

[Install]
WantedBy=multi-user.target

```

