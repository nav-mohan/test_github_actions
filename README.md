<h2> Example Github Actions to push files from Github Server to Remote DigitalOcean Server </h2>

<a href = 'https://zellwk.com/blog/github-actions-deploy/'>Link to original tutorial </a>
<p> Workflow config file is located at <code>./github/workflows/deploy.yml</code>. </p>
<p> Make this directory in your local repo <code> mkdir -pv ./github/workflows/ </code> </p>

```

name: "deploy"
on:
  push:
    branches:
      - "master"
jobs:
  build-and-deploy:
    runs-on: "ubuntu-latest"
    steps:
      - uses: "actions/checkout@v2"

      - name: "Install SSH key"
        uses: "shimataro/ssh-key-action@v2"
        with:
          key: ${{secrets.SSH_PRIVATE_KEY}}
          known_hosts: "unnecessary"
      
      - name: "Adding Known Hosts"
        run:  "ssh-keyscan -H ${{ secrets.SSH_HOST }} >> ~/.ssh/known_hosts"
      
      - name: "rsync over SSH"
        run:  "rsync -ar $source_dir ${{secrets.SSH_USER}}@${{secrets.SSH_HOST}}:$destination_dir --delete"
        env:
          source_dir: "./test_github_actions/"
          destination_dir: "/home/${{secrets.SSH_USER}}/dist/"


```

Secrets are included in the repository's ```Settings``` --> ```Secrets``` --> ```Actions```
<li><code>SSH_HOST</code> is the remote server's IP Address.</li>
<li><code>SSH_USER</code> is the user account on the remote server.</li>
<li><code>SSH_PRIVATE_KEY</code> is the remote server's private key. This is generated on the remote server by<br>
<ul><code>ssh-keygen -t rsa -b 4096 -C "your_email@example.com"</code></ul>
</li>
