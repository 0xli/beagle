### 2022/8/11 - 2022/8/14
#### set up a new rpc node for metamask and web3
- nginx conf
```
upstream ethprovider {
    server 127.0.0.1:8545 weight=1;
}

upstream swarmserver {
    server 127.0.0.1:8500 weight=1;
}

upstream apiserver {
    server 127.0.0.1:4000;
}

    location /eth {
        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Credentials' 'true';
        proxy_pass http://ethprovider/;
        proxy_http_version 1.1;
    }

```
- geth:--rpcdomain "*"
- geth:--rpchost "*"
  1. nginx report 403 error 
- Deploy ethereum-API on the same node of geth
- geth:--shh 
  1. report error 
```
      FaxTokenImAPI.web3.shh.newKeyPair((err, id) => {
        if (err) {
          console.log(`new shh keypair error`);
          console.log(err);
          reject(err)
        } else {
          resolve(id)
        }
      })

```
- geth: --wss --wssapi
```
nohup ../geth --datadir node1 --syncmode full --shh --rpc --rpcapi "shh,db,txpool,personal,ens,net,eth,web3"  --rpccorsdomain "*" --rpcvhosts "*"  --ws --wsport 8546 --wsapi "shh,personal,ens,net,eth,web3,db,txpool" --wsorigins "*"  --networkid 1515 --gasprice 1 --bootnodes "enode://eeddb5197f11b1c932b4613a937a9e2181e89a31a7526a661f146ce33414af4dfa5409639a24622559eb25f20fb16772ccdd81aa8d2764e2dbe77efa17d2295b@52.69.37.48:30301,enode://399c40c76434dbab54647d3310789e27bde00bb0ecff77c7d90efd76a862f99d6b2465c828d3eea59b7020329b765df7be480910e4e94fe0321e990aa6153eaa@118.190.79.30:30301" >node1.log 2>&1 &

```
- nginx weksocket
```
map $http_upgrade $connection_upgrade {
    default upgrade;
    '' close;
}

upstream websocket {
    server localhost:8546; # appserver_ip:ws_port
}

server {
     server_name geth.beagle.chat;
     listen 443 ssl;
     location / {
         proxy_pass http://websocket;
         proxy_read_timeout 300s;
         proxy_send_timeout 300s;

         proxy_set_header Host $host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

         proxy_http_version 1.1;
         proxy_set_header Upgrade $http_upgrade;
         proxy_set_header Connection $connection_upgrade;
     }
    ssl_certificate /home/ubuntu/.acme.sh/geth.beagle.chat/fullchain.cer;
    ssl_certificate_key /home/ubuntu/.acme.sh/geth.beagle.chat/geth.beagle.chat.key;
}


```
### 2022/8/15
- licode@callt 
  1. licode software all quit 
  2. scripts/initLicode.sh
- licode@kad  
  1. ssl certificate
  2. ssl wildcard for subdomains -d "*.kad.network"
  3. killerizo.sh
  4. script/initLicode.sh