# Docker Webhook Service

This container runs a phyton script that is listening for POST web requests on specified URLs and executes predetermined scripts/commands.

## Requirements
Make sure to create the **config.json** and **scripts** directory, preferably in the same directory as your *docker-compose*.

### Example docker-compose.yml
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
            - /var/run/docker.sock:/var/run/docker.sock

```

### Example config.json
```json
{
    "token": "YOUR_TOKEN",
    "hooks": {
        "HOOK_1": "scripts/YOUR_PROJECT/update.sh",
        "Captain_Hook": "scripts/ANOTHER_PROJECT/update.sh"
    }
}
```
Set the token and define as many hooks as you want.

## How to make scripts
In the scripts folder you will put the sh/bash scripts that do your desired actions.

If you use this to update docker stacks, I would recommend making a sub-directory with the update.sh and the docker-compose.yml .
### Example structure
```
/docker-webhooks/
    |- docker-compose.yml  ← For docker-webhooks
    |- config.json
    |- scripts/
        | - YOUR_PROJECT/
            |- update.sh
            |- docker-compose.yml  ← Your application stack
```
### Example *update.sh*
```bash
#!/bin/bash

# Sets working directory to script directory
SCRIPT_DIR=$(dirname "$0")
cd "$SCRIPT_DIR"

# Force pull new image
docker compose pull --policy always
# Force recreate all containers of this stack
docker compose up --force-recreate --detach
```
### Available tools for scripts
- sh
- bash
- curl
- wget
- docker
- python3

### ⚠️ Ensure your scripts are executable
You can set the executable flag with this command.
```bash
sudo chmod +x [path to script]
```
## Using the API
### Accessing the API
You have to do a **POST** request to your server using the tool of your choice. The URI format is the following:
```
http://your.domain:[PORT]/api?token=[TOKEN]&hook=[HOOK]
```

Here are some examples:

#### PowerShell
```powershell
Invoke-WebRequest -Method Post -Uri "http://your.domain:8000/api?token=YOUR_TOKEN&hook=YOUR_HOOK"
```
#### cURL
```bash
curl -X POST http://your.domain:8000/api?token=YOUR_TOKEN&hook=YOUR_HOOK
```

## Troubleshooting
Sometimes the API returns "path not found" refering to the script you tried to call with your hook. This error is also caused by wrong permissions on the script file and if the script returns an error. Sadly I can not pipe the script error to the API directly.