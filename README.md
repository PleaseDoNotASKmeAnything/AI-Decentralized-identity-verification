# 🟣 FaceAuth – Decentralized Facial Authentication System

FaceAuth is a complete **password + face recognition authentication system** powered by:

- **Django** (backend API)
- **Face Recognition + dlib** (face encoding)
- **Ethereum Blockchain (Ganache + Truffle)** for decentralized credential storage
- **Vanilla JavaScript + Camera API** (frontend)

This project captures a user’s face encoding, hashes it, and stores it *along with password hash* inside a **smart contract on a local blockchain**.

---

# 🚀 Features

### 🔐 Authentication
- Register with username + password + facial data  
- Login using password + live face scan  
- Blockchain verifies both hashes

### 🧠 Face Recognition
- dlib-based feature encoding  
- Generates 128-dimension facial embeddings  
- Encodings hashed before blockchain storage

### ⛓ Blockchain Storage
- Smart contract stores:
  - Username  
  - Password hash  
  - Face hash  
- No database needed — fully decentralized

### 📸 Frontend UI
- Webcam capture  
- Instant preview  
- Dashboard for stored credentials  

---

# 🧰 Prerequisites

| Tool | Required Version | Notes |
|------|-----------------|-------|
| **Python** | 3.9.x to 3.10.x | |
| **Node.js** | **18.x ONLY** | Truffle and Ganache **will fail** on Node 20 or 24+. If you have Node 20+, use NVM or Homebrew to install Node 18 (`brew install node@18`). |
| **npm** | Latest | |
| **Ganache CLI**| Local testnet | Installed via npm |
| **C++ Build Tools** | Required for dlib | Visual Studio (Windows) or CMake (Mac) |
| **Webcam** | Required | Needed for face capture |

---

# ⚙️ Installation Guide

## 1️⃣ Create Virtual Environment

First, navigate to your root project folder.

**💻 For Windows:**
```cmd
python -m venv venv
venv\Scripts\activate
```

**🍎 For macOS:**
```bash
python3 -m venv venv
source venv/bin/activate
```

---

## 2️⃣ Install dlib + face_recognition

**💻 For Windows Users:**
Download and install the precompiled wheel for Python 3.10:
```cmd
cd face_module
pip install dlib-19.22.99-cp310-cp310-win_amd64.whl
pip install git+https://github.com/ageitgey/face_recognition.git
pip install numpy Pillow opencv-python cmake
cd ..
```

**🍎 For macOS (Apple Silicon M1/M2/M3) Users:**
Because `pip` tends to hang while building `dlib` and modern macOS (Clang 17+) causes compilation errors with dlib's bundled C libraries, use this direct compilation method instead:

```bash
# 1. Install CMake
brew install cmake
pip install cmake

# 2. Download dlib directly from Github (bypasses pip hanging)
curl -L https://github.com/davisking/dlib/archive/refs/tags/v19.24.6.tar.gz -o dlib.tar.gz

# 3. Extract and patch the Mac compiler errors
tar -xzf dlib.tar.gz
cd dlib-19.24.6
sed -i '' 's/#      include <fp.h>/#      include <math.h>/g' dlib/external/libpng/pngpriv.h
sed -i '' '/define fdopen/d' dlib/external/zlib/zutil.h

# 4. Install dlib (You will see verbose C++ compilation progress. Takes 1-3 mins)
python3 setup.py install

# 5. Clean up and install the rest
cd ..
rm -rf dlib.tar.gz dlib-19.24.6
pip install face_recognition numpy Pillow opencv-python
```

---

## 3️⃣ Install Django dependencies

Ensure your virtual environment is still activated! 
*(Note: We install setuptools<70 because newer versions break the web3 library).*

```bash
cd backend
pip install "setuptools<70"
pip install -r requirements.txt
cd ..
```

---

## 4️⃣ Install Blockchain dependencies (Node.js)

*Make sure your terminal is using Node 18 before running this.*

```bash
cd blockchain
npm install
cd ..
```

---

# 🏃‍♂️ How to Run the Project (3 Terminals Required)

Once installed, you must run 3 separate terminal processes to bring the whole system online. Open 3 terminal tabs in the root project directory (`ai-decentralized-identity-verfication`).

### 🟩 Terminal 1: Start the Blockchain
*Environment: Normal Terminal (Must be Node 18)*

```bash
cd blockchain
npx ganache --port 7545 --deterministic
```
*(Leave this running)*

### 🟨 Terminal 2: Deploy Contract & Start Django Backend
*Environment: Python Virtual Environment (`venv`)*

**First, deploy the contract:**
```bash
cd blockchain
npx truffle compile
npx truffle migrate --reset
```
*Look at the output under `1_deploy_faceauth.js` and copy the `contract address` (e.g. `0xe78A0...`).*

**Next, update the code:**
Open `backend/authentication/views.py` and replace the `CONTRACT_ADDRESS` variable with the address you just copied.

**Finally, start the Django server:**
```bash
cd ..
# Windows: venv\Scripts\activate
# Mac: source venv/bin/activate
cd backend
python manage.py migrate
python manage.py runserver 8000
```
*(Leave this running)*

### 🟦 Terminal 3: Start the Frontend UI
*Environment: Normal Terminal*

```bash
cd frontend
# Windows: python -m http.server 3000
# Mac: python3 -m http.server 3000
```
*(Leave this running)*

---

# 🧪 Usage

Once all 3 terminals are running, open your browser to 👉 **http://localhost:3000/index.html**

## 🔵 Registration
1. Go to the **Register** tab  
2. Enter a username & password  
3. Allow camera permissions and capture your face  
4. Click **Register**  
5. Your data is encoded → hashed → stored on the local blockchain  

## 🟣 Login
1. Enter your username & password  
2. Capture your face  
3. Click **Login**  
4. The system will verify your hashes directly against the blockchain  

## 🟢 Dashboard
Upon successful login, you will see your stored credentials, including your face encoding and blockchain transaction response.
