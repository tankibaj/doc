# How to Forward Webhooks to Jenkins Behind a Firewall using ngrok



## Install ngrok

- Download ngrok

	```bash
	wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip -O ngrok.zip
	```

- Unzip to install

  ```bash
  unzip /path/to/ngrok.zip
  ```

- Move ngrok to bin

  ```bash
  sudo mv ngrok /usr/bin/
  ```

- Connect your account

  ```bash
  ngrok authtoken 1.........................k
  ```


- To start a HTTP tunnel forwarding to jenkins port 8080, run this next:

  ```bash
  ngrok http jenkins:8080
  ```

