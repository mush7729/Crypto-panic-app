# Crypto-panic-app
# 1️⃣ Download the Flutter project ZIP from ChatGPT sandbox
curl -L -o crypto.zip "https://chat.openai.com/sandbox/file/crypto_panic_app_FINAL.zip"

# 2️⃣ Unzip the project into the current folder
unzip crypto.zip -d .

# 3️⃣ Remove the zip to keep repo clean
rm crypto.zip

# 4️⃣ Stage all files for Git
git add .

# 5️⃣ Commit files with a message
git commit -m "Add Flutter Crypto Panic App"

# 6️⃣ Push the code to GitHub
git push
