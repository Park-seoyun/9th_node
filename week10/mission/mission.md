/.github/workflows/deploy-main.yml

```jsx
name: deploy-main

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Configure SSH
        run: |
          mkdir -p ~/.ssh
          echo "$EC2_SSH_KEY" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa

          cat >>~/.ssh/config <<END
          Host umc-server
            HostName $EC2_HOST
            User ubuntu
            IdentityFile ~/.ssh/id_rsa
            StrictHostKeyChecking no
          END
        env:
          EC2_HOST: ${{ secrets.EC2_HOST }}
          EC2_SSH_KEY: ${{ secrets.EC2_SSH_KEY }}

      - name: Copy Workspace
        run: |
          ssh umc-server 'sudo mkdir -p /opt/app'
          ssh umc-server 'sudo chown ubuntu:ubuntu /opt/app'
          scp -r ./[!.]* umc-server:/opt/app

      - name: Install dependencies
        run: |
          ssh umc-server 'cd /opt/app && npm install'
          ssh umc-server 'cd /opt/app && npx prisma generate'

      - name: Copy systemd service file
        run: |
          ssh umc-server '
            echo "[Unit]
            Description=UMC Workbook Project
            After=network.target

            [Service]
            User=ubuntu
            WorkingDirectory=/opt/app
            ExecStart=/usr/bin/npm run start --prefix /opt/app/
            Restart=always
            Environment=NODE_ENV=production

            [Install]
            WantedBy=multi-user.target" | sudo tee /etc/systemd/system/app.service
          '

      - name: Enable and restart service
        run: |
          ssh umc-server 'sudo systemctl daemon-reload'
          ssh umc-server 'sudo systemctl enable app'
          ssh umc-server 'sudo systemctl restart app'
```

오류 : SSH 연결 타임아웃 → ssh 모든 ip 허용

ec2에 .env 파일생성

sudo nano /opt/app/.env

prisma 마이그레이션

cd /opt/app && npx prisma migrate deploy

EC2 보안 그룹 → Custom TCP / 3001 / 0.0.0.0/0 추가