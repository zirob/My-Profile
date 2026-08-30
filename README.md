# My Profile

Personal portfolio website showcasing my work experience, projects, technical skills, and hobbies.

## Project Goal

The goal of this project is to build my personal portfolio while gaining hands-on experience with AWS, Linux, networking, Git, and DevOps practices.

The idea is building and configuring the infrastructure step by step.

## AWS Infrastructure and Deploytment

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

### Configuration

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

* boot of Nginx in EC2 inctance. OK
* Checking of Access by http. OK

### Security Improvement

After confirming Session Manager access, I removed the inbound SSH rule for port 22 from the EC2 Security Group.

The instance can now be administered without exposing SSH directly to the internet.

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

## Releases

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
