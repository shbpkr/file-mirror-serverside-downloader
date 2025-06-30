# PHP Server-Side File Downloader & Proxy Browser

This is a powerful, self-contained PHP application that allows you to download files directly to your web server and browse websites through a server-side proxy. It is designed to handle large files and overcome common server limitations by using a persistent `wget` process and an automated "watchdog" script to restart stalled downloads.

> ## :warning: VERY IMPORTANT WARNING :warning:
>
> This is a powerful tool. Its behavior can be flagged as **malicious** by shared hosting providers. The script executes server commands (`wget`) and makes outbound connections to any URL you provide.
>
> * **USE AT YOUR OWN RISK.** This script can easily violate your hosting provider's Terms of Service.
> * **DO NOT USE ON STANDARD SHARED HOSTING.** As discovered during development, most shared hosts will shut down your site for using this script.
> * This tool is intended for use on a **Virtual Private Server (VPS)** or a **Dedicated Server** where you have full control and permission to run persistent background processes.

---

## Features

-   **Direct Downloader:** Paste any direct HTTP link to download a file.
-   **Proxy Browser:** Enter a website URL to browse it from the server. The script automatically identifies download links and rewrites them to use the server-side downloader.
-   **Persistent Downloads:** Uses `nohup` and `wget` with infinite retries to ensure large downloads complete, even if the connection is interrupted.
-   **Automated Watchdog:** Includes a `watcher.php` script that can be run on a schedule (cron job) to automatically find and restart any stalled downloads.
-   **Password Protection:** The entire application is protected by a simple passkey you can set.
-   **Live Status Monitoring:** See the live output of `wget` to monitor download progress in real-time.

## Requirements

-   A web server (VPS or Dedicated recommended).
-   PHP 7.4 or newer with the `curl` extension.
-   The ability to execute `shell_exec()` from PHP.
-   `wget` utility installed on the server.
-   The ability to run Scheduled Tasks (cron jobs).

## Installation & Setup

Follow these steps carefully to set up the application.

### 1. File Structure

First, upload the files to a directory on your web server. Your final file structure should look like this:

/your-web-directory/|-- index.php         (The main application)|-- watcher.php       (The automated watchdog script)|-- files/            (This directory will be created automatically)
### 2. Configuration

You **must** edit both `index.php` and `watcher.php` to set your own passwords and configurations.

**In `index.php`:**

-   **`$passkey`**: Change `'onelife'` to your own secret password. This is what you will use to log into the main application.

**In `watcher.php`:**

-   **`$cron_secret_key`**: Change `'a1b2c3d4e5'` to a long, random string. This is a security key to prevent anyone else from running your watcher script.

### 3. Directory Permissions

The script will attempt to create the `/files/` directory for you. If it fails, you may need to create it manually and set its permissions to `755` or `777`.

### 4. Set Up the Watchdog (Scheduled Task / Cron Job)

The watchdog script is essential for ensuring downloads don't stay paused. You need to create a scheduled task that automatically "visits" the `watcher.php` URL.

Go to your server's "Scheduled Tasks" or "Cron Job" control panel and create a new task with the following settings:

-   **Schedule:** Run **Every 15 minutes** or **Every 20 minutes**.

-   **Command:** Use the `wget` command below. You must replace the URL and the secret key with your own.

    ```bash
    wget -q -O - "[https://your-domain.com/path/to/watcher.php?cron_key=YOUR_SECRET_KEY](https://your-domain.com/path/to/watcher.php?cron_key=YOUR_SECRET_KEY)" > /dev/null 2>&1
    ```

    *Example:*
    ```bash
    wget -q -O - "[https://parvrish.com/donloder/watcher.php?cron_key=a1b2c3d4e5](https://parvrish.com/donloder/watcher.php?cron_key=a1b2c3d4e5)" > /dev/null 2>&1
    ```

    *(**Why use `wget` for the cron job?** Some hosting environments restrict what scripts can do when run directly from the command line. By using `wget` to visit the URL, the script is executed by the web server, which has the correct permissions.)*

## Usage

1.  Navigate to `your-domain.com/path/to/index.php`.
2.  Enter the passkey you set in `index.php`.
3.  Use the "Direct Downloader" tab to paste a direct link and start a download.
4.  Use the "Proxy Browser" tab to browse a website. Any link that points to a downloadable file will be highlighted in red. Clicking it will automatically start a server-side download.
5.  If a download was started previously, you can click the "Check Status" button in the "Ongoing Downloads" section to resume monitoring it.

## Disclaimer

The author is not responsible for any misuse of this script or for any violations of your hosting provider's terms of service. Use this tool responsibly and only on servers where you have explicit permission to do so.
