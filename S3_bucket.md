
# **AWS S3 Bucket Setup Guide**<br>

## **Step 1: Create an S3 Bucket**<br>
1. **Go to AWS Console** → **Search "S3"**  <br>
2. **Create a New Bucket**  <br>
   - Enter a **Bucket Name**  <br>
   - Enable **ACL (Access Control List)**  <br>
   - **Uncheck** "Block Public Access"  <br>
   - **Acknowledge** the checkmark  <br>
   - Click **Create Bucket**  <br>

---

## **Step 2: Upload Files to S3**<br>
1. Open the newly created **S3 Bucket** <br> 
2. Click **"Upload"** → **Add Files or Folder**  <br>
3. Click **"Upload"** to save the files  <br>

---

## **Step 3: Test File Accessibility**<br>
1. **Go to the uploaded file**  <br>
2. Click on the **File URL** → **Open in a new tab**  <br>
   - **You won’t be able to access it yet** (Access Denied)  <br>

---

## **Step 4: Update File Permissions to Make It Public**<br>
1. Go back to the **S3 Bucket**  <br>
2. Select the **file** → Click **Edit Permissions**  <br>
3. Under **"Permissions"** → **Grant "Everyone" Read Access**  <br>
4. Click **Save Changes**  <br>

---

## **Step 5: Verify Public Access**<br>
1. Click on the **File URL** again → Open in a new tab  <br>
   - **Now, the file should be accessible**  <br> 

---

## **Step 6: Launch an EC2 Instance**<br>
1. Go to **AWS Console** → **Search "EC2"**  <br>
2. Click **"Launch Instance"**  <br>
3. Configure instance settings as required  <br>
4. Click **"Launch"**  <br>

---

## **Step 7: Install Dependencies on EC2**<br>
Open the **Terminal** and execute the following commands:<br>

```sh
yum install automake fuse fuse-devel gcc-c++ git libcurl-devel libxml2-devel make openssl-devel
git clone https://github.com/s3fs-fuse/s3fs-fuse.git
ls -la
cd s3fs-fuse
./autogen.sh
./configure --prefix=/usr --with-openssl
make
sudo make install
which s3fs
```
## **Step 8: Create an IAM User**<br>
1. **Go to AWS Console** → **Search "IAM"**  <br>
2. Click **"Create User"**  <br>
   - Enter **Username**  <br>
   - Attach **Policy** (Select Round Option)  <br>
3. **Search and Select "AmazonS3FullAccess"**  <br>
4. Click **Next** → **Create User**  <br>

---

## **Step 9: Generate Access Keys**<br>
1. Click on the created **IAM User**  <br>
2. Go to **Security Credentials** → **Create Access Key**  <br>
3. Select **"Application running on AWS"** <br> 
4. **Acknowledge** and Click **Next**  <br>
5. Add **Description Tag** (Optional)  <br>
6. Click **Create Access Key**  
7. **Download the `.csv` file** (Contains Access & Secret Keys)  <br>

---

## **Step 10: Enable Multi-Factor Authentication (MFA)**<br>
### **In AWS Console:**<br>
1. **Search "IAM"**  <br>
2. Click on the **IAM User** <br>  
3. Go to **Security Credentials** → **Add MFA**  <br>
4. Select **"Virtual MFA Device"**  <br>

### **On Your Mobile Device:**<br>
1. **Open Google Authenticator App** <br> 
2. **Scan the QR Code** <br>

### **Back to AWS Console:**<br>
1. **Enter MFA Code 1** (Generated in App)  <br>
2. **Wait ~30 seconds**  <br>
3. **Enter MFA Code 2**  <br>
4. Click **Add MFA**<br>

## **Step 11: Configure S3FS Credentials**<br>
1. Open **Terminal** and create a password file:  
   ```sh
   touch /etc/passwd-s3fs
   vim /etc/passwd-s3fs
   ```

## **Step 12: Configure S3FS Credentials**<br>
1. Open the **.csv file** downloaded from AWS IAM.  
2. Copy the **first key** and **second key** from the file.  
3. Open the .csv file downloaded before and copy the keys in the format **ACCESS_KEY:SECRET_KEY** to paste it in vim editor:  
   ```sh
   touch /etc/passwd-s3fs
   vim /etc/passwd-s3fs
```

## **Step 13: Set Correct Permissions**<br>
Secure the credentials file:  
```sh
chmod 640 /etc/passwd-s3fs
```

## **Step 14: Create a Mount Directory**<br>
Create a directory for the S3 bucket mount:  
```sh
mkdir /mys3bucket
```

## **Step 15: Mount the S3 Bucket**<br>
Use s3fs to mount the S3 bucket to the directory:  

```sh
s3fs yourbucketname -o use_cache=/tmp -o allow_other -o uid=1001 -o mp_umask=002 -o multireq_max=5 /mys3bucket
```

## **Step 16: Verify Mount Status**<br>

```sh
df -h
cd /mys3bucket/
ls -la
```






  
