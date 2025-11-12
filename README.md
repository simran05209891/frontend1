name: Frontend Deploy

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      # ✅ Get Code From GitHub Repo
      - name: Checkout Code
        uses: actions/checkout@v3

      # ✅ Install Node.js
      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: 18

      # ✅ Install Dependencies
      - name: Install Dependencies
        run: npm install

      # ✅ Build React App
      - name: Build React App
        run: npm run build

      # ✅ SSH Key Setup
      - name: Setup SSH
        uses: webfactory/ssh-agent@v0.7.0
        with:
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}

      # ✅ Upload Build Folder To Server
      - name: Upload Build To Server
        run: |
          ssh -o StrictHostKeyChecking=no simran@122.160.115.189 "sudo rm -rf /var/www/frontend/*"
          scp -o StrictHostKeyChecking=no -r build/* simran@122.160.115.189:/var/www/frontend/

      # ✅ Restart Nginx
      - name: Restart Nginx
        run: |
          ssh -o StrictHostKeyChecking=no simran@122.160.115.189 "sudo systemctl restart nginx"
