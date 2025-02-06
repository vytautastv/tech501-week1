# Setting Up a Jenkins Job

## Prerequisites

1. **Jenkins Installed**: Ensure Jenkins is installed and accessible via a web interface.
2. **Jenkins User Permissions**: Make sure your Jenkins user has the necessary permissions to configure jobs and access the required repositories.
3. **GitHub Access**: You should have SSH credentials set up for GitHub to allow Jenkins to clone/push to repositories.

---

## Steps to Create and Configure a Jenkins Job

### 1. **Access Jenkins Dashboard**

   - Open your Jenkins web interface (e.g., `http://<your-jenkins-ip>:8080`).
   - Log in with your Jenkins credentials.

---

### 2. **Create a New Job**

   1. From the Jenkins dashboard, click **"New Item"**.
   2. Provide a **name** for the job (e.g., `my-first-job`).
   3. Select the type of job you want to create:
      - **Freestyle project**: Simple, easy-to-configure job.
      - **Pipeline**: More complex, allows integration with pipeline scripts.
   4. Click **OK** to proceed.

---

### 3. **Configure Source Code Management (SCM)**

   1. In the job configuration page, scroll down to **Source Code Management**.
   2. Select **Git**.
   3. Enter the **repository URL**:
      - If using SSH (recommended for private repositories), the URL should be in this format:
        `git@github.com:username/repository.git`
   4. For **credentials**, select the appropriate GitHub credentials (ensure that the private key is set up).
      - If you don't have credentials set up, click **Add** and enter your GitHub credentials.

---

### 4. **Configure Build Triggers**

   To automatically trigger the build based on events like code commits:

   1. Scroll down to **Build Triggers**.
   2. Select **GitHub hook trigger for GITScm polling** to trigger builds from GitHub webhook events.
   3. Alternatively, you can set up periodic builds using the **Build periodically** option.

---

### 5. **Configure Build Environment**

   To set up build environment variables, tools, or plugins:

   1. Scroll to **Build Environment**.
   2. Check **Use secret text(s) or file(s)** if you want to add environment variables like API keys or credentials.
   3. Check **Delete workspace before build starts** if you want to clear the workspace before each build.

---

### 6. **Configure Build Steps**

   1. Scroll to **Build**.
   2. Click **Add build step** and choose the appropriate action:
      - **Execute shell**: If you want to run a shell command or script. Example:
        ```bash
        echo "Building my app"
        npm install
        npm test
        ```
      - **Invoke Gradle script** (for Java-based projects), or other steps depending on your project type.

---

### 7. **Configure Post-build Actions**

   You can define post-build actions, such as notifications, archiving artifacts, etc.:

   1. Scroll to **Post-build Actions**.
   2. Choose actions like:
      - **Email Notifications**: Configure Jenkins to send an email upon build completion.
      - **Archive Artifacts**: Archive build output (e.g., `.tar`, `.zip` files).
      - **Deploy**: Deploy the code to a server or cloud service after the build.

---

### 8. **Save the Job**

   After all configurations are done, scroll to the bottom and click **Save**.

---

### 9. **Configure GitHub Webhook**

   To trigger Jenkins builds on commits to GitHub:

   1. Go to your repository on GitHub.
   2. Navigate to **Settings > Webhooks**.
   3. Click **Add webhook** and enter your Jenkins webhook URL in this format:
      `http://<jenkins-url>/github-webhook/`
   4. Set the content type to `application/json` and choose the events to trigger (e.g., **Push events**).
   5. Click **Add webhook**.

---

## Troubleshooting

- **Permission Issues**: Ensure Jenkins has the correct SSH key and GitHub credentials.
- **Git Pull Error**: If you get an error like `Permission denied (publickey)`, double-check your SSH keys in GitHub and Jenkins credentials.
- **Failed Builds**: Check the Jenkins build logs to identify issues in the build step or post-build action.

---
