# Docker Webhook Service

This container runs a phyton script that is listening for POST web requests on specified URLs and executes predetermined scripts/commands.

## Requirements
Make sure to create the **config.json** and **scripts** directory, preferably in the same directory as your *docker-compose*.

### docker-compose example
```yaml
name: docker-webhooks
services:
    docker-webhooks:
        container_name: docker-webhooks
        image: ghcr.io/rlanger2000/docker-webhooks:latest
        ports:
            - "8000:8000"
        volumes:
            - ./scripts/:/srv/scripts
            - ./config.json:/srv/config.json
```

### config.json example
```json
{
    "token": "CHANGE_ME",
    "hooks": {
        "test": "scripts/cool_script.sh"
    }
}
```
Set the token and define as many hooks as you want.

### Scripts folder permissions
Absolutely make sure your scripts inside the scripts folder (on your host) have the executable permission flag. You can achieve this by doing this command on the entire scripts folder.
```bash
sudo -r chmod +x [path to scripts folder]
```
## Using the API
### Accessing the API
You have to do a **POST** request to your server using the tool of your choice. The URI format is the following:
```
http://your.domain:[PORT]/api?token=[TOKEN]&hook=[HOOK]
```

Here are some examples.

#### PowerShell
```powershell
$token="YOUR_TOKEN"
$hook="YOUR_HOOK"
Invoke-WebRequest -Method Post -Uri "http://your.domain:8000/api?token=$token&hook=$hook"
```
#### cURL
```bash
curl -X POST -d token=YOUR_TOKEN -d hook=YOUR_HOOK http://your.domain:8000/api
```

## Troubleshooting
Sometimes the API returns "path not found" refering to the script you tried to call with your hook. This error is also caused by wrong permissions on the script file and if the script returns an error. Sadly I can not pipe the script error to the API directly.