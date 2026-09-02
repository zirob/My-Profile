# My Profile

Personal portfolio website showcasing my work experience, projects, technical skills, and hobbies.

## Project Goal

The goal of this project is to build my personal portfolio while gaining hands-on experience with AWS, Linux, networking, Git, and DevOps practices.

The idea is building and configuring the infrastructure step by step.

## AWS Infrastructure and Deployment

### Amazon EC2

* Created an Amazon EC2 instance to host the web server.
* Configured SSH access to the EC2 instance.
* Created and configured an EC2 key pair for authentication.
* Secured the private `.pem` key with appropriate file permissions.
* Connected to the instance using SSH.

Example:

```bash
chmod 400 my-web-server-key.pem
ssh -i my-web-server-key.pem ec2-user@<EC2-IP>
```

### Elastic IP

* Allocated an Elastic IP address.
* Associated the Elastic IP with the EC2 instance.
* Configured my local SSH client to use the Elastic IP.

### EC2 Access with AWS Systems Manager Session Manager

Initially, I accessed the EC2 instance directly through SSH using port 22 and a Security Group rule restricted to my public IP address.

Because my ISP assigns a dynamic public IP, SSH access stopped working whenever my public IP changed.

To avoid depending on a changing public IP and to remove inbound SSH access, I migrated to AWS Systems Manager Session Manager.

### Access Flow

```
Mac
 ↓
AWS CLI
 ↓
aws login
 ↓
AWS Systems Manager Session Manager
 ↓
EC2 instance
 ↓
ssm-user
```

### Access Configuration

* Attached an IAM role to the EC2 instance with the AmazonSSMManagedInstanceCore policy.
* Verified that Session Manager worked through the AWS Console.
* Installed the Session Manager plugin on macOS.
* Updated AWS CLI to a version that supports aws login.
* Authenticated the AWS CLI using temporary credentials:
```
aws login
```
* Verified the authenticated IAM identity:
```
aws sts get-caller-identity
```
* Connected to the EC2 instance:
```
aws ssm start-session \
  --target <INSTANCE_ID> \
  --region us-east-2
```

### Nginx

* Installed and started Nginx on the EC2 instance.
* Linked Nginx's web root to the cloned project directory:

```bash
sudo ln -s /var/www/My-Profile/ /usr/share/nginx/html
```

* Verified that the portfolio is served successfully from the instance's root HTTP URL.
```text
http://<EC2-ELASTIC-IP>/
```
### Domain and DNS

The portfolio is publicly accessible through a custom domain secured with HTTPS.

### Domain Registration

The domain was registered with **Porkbun**:

```text
borisramirez.com
```

Porkbun is used as the domain registrar, while **Amazon Route 53** is used as the authoritative DNS service.

The Route 53 nameservers assigned to the hosted zone were configured in Porkbun so that DNS management is delegated to AWS.

```text
Porkbun
   │
   │ Nameserver delegation
   ▼
Route 53
```

### Route 53 Hosted Zone

A **Public Hosted Zone** was created in Amazon Route 53 for:

```text
borisramirez.com
```

Route 53 automatically created the required **NS** and **SOA** records.

The DNS configuration was verified from the terminal using:

```bash
dig NS borisramirez.com
```

This command queries DNS for the authoritative nameservers associated with the domain.

### DNS Records

An **A record** was created to point the root domain to the EC2 Elastic IP:

```text
Type:  A
Name:  borisramirez.com
Value: <EC2_ELASTIC_IP>
TTL:   300
```

The DNS resolution can be verified with:

```bash
dig borisramirez.com
```

The expected result is the Elastic IP assigned to the EC2 instance.

The resolution flow is:

```text
borisramirez.com
      │
      │ A record
      ▼
EC2 Elastic IP
```
### WWW Domain — CNAME Record

A **CNAME record** was created for the `www` hostname:

```text
Type:  CNAME
Name:  www.borisramirez.com
Value: borisramirez.com
TTL:   300
```

The CNAME makes `www.borisramirez.com` an alias of the root domain.

```text
www.borisramirez.com
        │
        │ CNAME
        ▼
borisramirez.com
        │
        │ A record
        ▼
EC2 Elastic IP
```

This allows both hostnames to reach the same web server:

```text
borisramirez.com
www.borisramirez.com
```

### Nginx Domain Configuration

Nginx was configured to recognize both domain names.

The `server_name` directive contains:

```nginx
server_name borisramirez.com www.borisramirez.com;
```

The Nginx configuration can be validated before applying changes:

```bash
sudo nginx -t
```

Then the configuration can be reloaded without stopping the web server:

```bash
sudo systemctl reload nginx
```
### HTTPS with Let's Encrypt and Certbot

HTTPS is implemented using a TLS certificate issued by **Let's Encrypt** and managed with **Certbot**.

The required packages were installed on Amazon Linux 2023:

```bash
sudo dnf install -y certbot python3-certbot-nginx
```

The certificate was initially requested for the root domain:

```bash
sudo certbot --nginx -d borisramirez.com
```

The certificate was later expanded to protect both hostnames:

```bash
sudo certbot --nginx \
  -d borisramirez.com \
  -d www.borisramirez.com
```

Certbot performs several tasks automatically:

1. Communicates with Let's Encrypt.
2. Validates control of the domain.
3. Requests the TLS certificate.
4. Installs the certificate for Nginx.
5. Configures automatic certificate renewal.

### TLS Certificate

The certificate files are stored under:

```text
/etc/letsencrypt/live/borisramirez.com/
```

The main files used by Nginx are:

```text
fullchain.pem
privkey.pem
```

Nginx references them with configuration similar to:

```nginx
ssl_certificate /etc/letsencrypt/live/borisramirez.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/borisramirez.com/privkey.pem;
```

The certificate secures communication between the browser and Nginx:

```text
Browser
   │
   │ TLS encrypted connection
   ▼
HTTPS :443
   │
   ▼
Nginx
```

### Security Group

The EC2 Security Group allows public web traffic on:

| Protocol | Port | Purpose |
|---|---:|---|
| HTTP | 80 | Web traffic and HTTP validation/redirect |
| HTTPS | 443 | Encrypted web traffic |

Public SSH access on port `22` is not required for administration because the instance is managed through **AWS Systems Manager Session Manager**.


## Technologies Used

* AWS
* Amazon EC2
* Linux
* SSH
* Git
* GitHub
* HTML
* CSS
* JavaScript

### Final Deployment Architecture

```text
Internet
   ↓
borisramirez.com / www.borisramirez.com
   ↓
Porkbun (Domain Registrar)
   ↓ DNS delegation
Amazon Route 53
   ↓
18.188.73.65 (Elastic IP)
   ↓
Amazon EC2
   ↓
Nginx + TLS Certificate
   ↓
/usr/share/nginx/html
   ↓ symbolic link
/var/www/My-Profile

```

## Releases

### v1.1.0 — Portfolio Visual Update

This version improves the homepage presentation and makes the featured project easier to access while preserving the original structure and functionality introduced in `v1.0.0`.

* Added a responsive profile photo beside the name in the hero section.
* Converted the profile photo to WebP format (`img/Photo_of_Portfolio.webp`) to reduce its file size and improve page loading performance.
* Refined the photo crop, size, framing, and subtle grey background shadow.
* Centered the desktop navigation while preserving the existing language selector and responsive mobile menu.
* Added a direct GitHub link from project 01 to the [`My-Profile` repository](https://github.com/zirob/My-Profile).
* Added bilingual text for the new GitHub repository link.
* Introduced the AWS orange color (`#FF9900`) for the hero action buttons.
* Applied the same orange accent only to the currently selected language; the unselected language remains grey.
* Preserved visible keyboard-focus feedback for the language controls.

### v1.0.0 — First Version

 The first version of the portfolio homepage was built without frameworks, using semantic HTML, CSS, and vanilla JavaScript. The code is separated by responsibility to keep the project simple and easy to maintain.
 
* Initial responsive website layout
* About Me section
* Professional experience
* Skills and technologies
* Personal projects
* Hobbies and interests
* Prepared for deployment on AWS EC2 with Nginx

### Project Structure

```text
My-Profile/
├── index.html
├── css/
│   └── styles.css
├── js/
│   └── script.js
├── img/
├── README.md
└── .gitignore
```

### Homepage Sections

The single-page homepage includes:

* A hero section with a reflective introduction, professional focus, and navigation links.
* An About Me section describing my professional transition toward Cloud and DevOps, my approach to learning, and my personal values.
* A Work Experience section using a timeline layout with independent entries for current learning and previous professional roles.
* A Projects section highlighting the AWS infrastructure created for this portfolio. The card layout supports additional projects as they are added.
* A Skills section organized by cloud, systems, development, and practices.
* A Hobbies section covering technology, continuous learning, personal projects, family time, travel, sports, football, movies, reading, and drawing manga.
* A footer with an automatically updated copyright year.

### Bilingual Content

The complete portfolio is available in English and Spanish. The language selector is located in the header and updates the visible content without reloading the page.

Both language versions include the same:

* Introduction and About Me content.
* Professional experience, dates, roles, companies, and responsibilities.
* Project descriptions.
* Skills and hobbies.
* Navigation, metadata, accessibility labels, and footer text.

Translations are stored in the `translations` object in `js/script.js`. Each translatable HTML element uses a `data-i18n` attribute whose value corresponds to a translation key.

Example:

```html
<h2 data-i18n="aboutTitle">Building a new chapter</h2>
```

```javascript
en: { aboutTitle: "Building a new chapter" }
es: { aboutTitle: "Construyendo una nueva etapa" }
```

### Responsive Design

The layout adapts to desktop, tablet, and mobile screen sizes. On smaller screens, the navigation becomes a collapsible menu and multi-column content changes to a single-column layout.

The stylesheet also includes reduced-motion support for users who enable that accessibility preference in their operating system.

### JavaScript

The JavaScript is intentionally lightweight and is used to:

* Open and close the navigation menu on mobile devices.
* Close the mobile menu after a navigation link is selected.
* Set the current year in the footer automatically.
* Switch all portfolio content, page metadata, and accessibility labels between English and Spanish.
* Save the selected language in the browser so it is preserved on future visits.

### Running Locally

No installation or build process is required. Open `index.html` directly in a web browser, or serve the project with a local web server.

For example, with Python installed:

```bash
python3 -m http.server 8000
```

Then visit `http://localhost:8000` in a browser.

### Customization

The English and Spanish portfolio content should be updated in the `en` and `es` sections of the `translations` object in `js/script.js`. The `data-i18n` attributes in `index.html` connect page elements to those translations.

Page structure is maintained in `index.html`, visual styles and responsive rules are stored in `css/styles.css`, and interactive and translation behavior is stored in `js/script.js`. Future images and other visual assets can be placed in the `img/` directory.
