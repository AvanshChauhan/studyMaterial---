# Nodemailer Setup with Gmail App Password

This guide explains how to configure Nodemailer using a Gmail App Password in a Node.js application.

## Prerequisites

* A Gmail account
* 2-Step Verification enabled on your Google account
* Node.js installed

---

## Step 1: Enable 2-Step Verification

1. Open Google Account Settings.
2. Navigate to **Security**.
3. Enable **2-Step Verification**.

Without 2-Step Verification, App Passwords are not available.

---

## Step 2: Generate an App Password

1. Open **Google Account → Security → App Passwords**.
2. Select or create an app name (e.g., "Perplex Chat App").
3. Click **Generate**.
4. Copy the generated 16-character password.

Example:

```text
abcd efgh ijkl mnop
```

---

## Step 3: Install Dependencies

```bash
npm install nodemailer dotenv
```

---

## Step 4: Configure Environment Variables

Create a `.env` file:

```env
GOOGLE_USER=your-email@gmail.com
GOOGLE_APP_PASSWORD=abcdefghijklmnop
```

Never commit your `.env` file to GitHub.

---

## Step 5: Create Mail Service

Create `src/services/mail.service.js`:

```js
import nodemailer from "nodemailer";
import dotenv from "dotenv";

dotenv.config();

const transporter = nodemailer.createTransport({
    service: "gmail",
    auth: {
        user: process.env.GOOGLE_USER,
        pass: process.env.GOOGLE_APP_PASSWORD,
    },
});

export async function sendEmail({ to, subject, text, html }) {
    const mailOptions = {
        from: process.env.GOOGLE_USER,
        to,
        subject,
        text,
        html,
    };

    return await transporter.sendMail(mailOptions);
}
```

---

## Step 6: Send a Test Email

```js
import { sendEmail } from "./services/mail.service.js";

await sendEmail({
    to: "recipient@example.com",
    subject: "Test Email",
    text: "Hello from Nodemailer!",
});
```

---

## Example: Welcome Email

```js
await sendEmail({
    to: email,
    subject: "Welcome to Perplex",
    text: `Hello ${username},

Welcome to Perplex!

Your account has been successfully created.

Thank you for joining us.

Best regards,
Perplex Team`
});
```

---

## Common Errors

### 535 Username and Password not accepted

Cause:

* Incorrect App Password
* 2-Step Verification not enabled
* Wrong Gmail account

Solution:

* Regenerate App Password
* Verify Gmail address
* Confirm 2-Step Verification is enabled

### 530 Authentication Required

Cause:

* Missing credentials
* Incorrect environment variables

Solution:

* Verify `.env` values
* Restart the server after updating `.env`

---

## Security Notes

* Never expose your App Password.
* Never commit `.env` files.
* Use a dedicated Gmail account for project emails.
* Add `.env` to `.gitignore`.

```gitignore
.env
```

---

## Recommended Project Structure

```text
src/
├── controllers/
├── models/
├── routes/
├── services/
│   └── mail.service.js
├── app.js
└── server.js
```

Your application is now ready to send emails using Gmail and Nodemailer.
