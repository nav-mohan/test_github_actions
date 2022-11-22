Workflow is located inside ```./github/workflows/deploy.yml```. Make this directory in your local repository
```mkdir -pv ./github/workflows```


```

name: deploy
on:
  push:
    branches:
      - main
      - master
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Install SSH key
        uses: shimataro/ssh-key-action@v2
        with:
          key: ${{secrets.SSH_PRIVATE_KEY}}
          known_hosts: "unnecessary"
      
      - name: "Adding Known Hosts"
        run:  "ssh-keyscan -H ${{ secrets.SSH_HOST }} >> 
~/.ssh/known_hosts"
      
      - name: "rsync over SSH"
      # this doesn't seem to point to the right directory. try just scp?
        run:  "rsync -ar ../ alice@${{secrets.SSH_HOST}}:/home/alice/"
      # another hackey way would be to place a shell script on
      # the remote server which pulls from github and then execute that 
shell script 
      # through github actions by sshing into the remote server??
        

```

Secrets are included in the repository's ```Settings```-->```Secrets```-->```Actions```
..* ```SSH_HOST``` is the remote DigitalOcean's IP Address
..* ```SSH_PRIVATE_KEY``` is the remote server's private key. This is generated on the remote server by 
... ```ssh-keygen -t rsa -b 4096 -C "your_email@example.com"```

