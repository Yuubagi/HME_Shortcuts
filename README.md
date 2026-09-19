# iCloud Hide My Email Generator for iOS Shortcuts

Generate iCloud Hide My Email addresses directly from Apple Shortcuts.

Once configured, you can generate a new Hide My Email address, assign a custom label, reserve it in your iCloud account, and automatically copy it to your clipboard without opening iCloud.com.

## Features

* Generate Hide My Email addresses from iOS Shortcuts
* Reserve generated addresses automatically
* Enter a custom label for each address
* Automatically copy the generated address to the clipboard
* Store configuration locally in iCloud Drive
* Update only the iCloud session cookie when it expires
* No external server required
* No Apple ID password is stored in the shortcuts

## Shortcuts

### HME Setup

Initial configuration and authentication check.

[Install HME Setup](https://www.icloud.com/shortcuts/13a758920d0e44c3952f2d72cb596160)

### HME Generate

Generates and reserves a new Hide My Email address.

[Install HME Generate](https://www.icloud.com/shortcuts/13a758920d0e44c3952f2d72cb596160)

### HME Update Cookie

Updates the saved iCloud session cookie without repeating the full setup.

[Install HME Update Cookie](https://www.icloud.com/shortcuts/13a758920d0e44c3952f2d72cb596160)

## Requirements

* iPhone or iPad
* Apple Shortcuts
* iCloud+ with Hide My Email enabled
* A desktop browser logged into iCloud.com
* Access to the browser Developer Tools

## Initial Setup

### 1. Install the shortcuts

Install:

1. HME Setup
2. HME Generate
3. HME Update Cookie

Run **HME Setup** first.

## 2. Open Hide My Email on iCloud.com

On a desktop browser:

1. Sign in to iCloud.com.
2. Open iCloud+.
3. Open **Hide My Email**.
4. Open Developer Tools.

   * Chrome / Edge: `F12`
   * macOS browsers may use a different shortcut.
5. Open the **Network** tab.
6. Reload or reopen Hide My Email.

## 3. Find the HME list request

In the Network panel, search for:

```text
hme/list
```

Look for a request similar to:

```text
https://pXXX-maildomainws.icloud.com/v2/hme/list?clientBuildNumber=XXXX&clientMasteringNumber=XXXX&clientId=XXXX&dsid=XXXX
```

Copy the complete **Request URL**.

Do not manually modify the URL.

The URL contains information specific to your iCloud account and is used by the shortcuts to construct the Generate and Reserve API requests.

## 4. Obtain your iCloud Cookie Header String

Find the same `/v2/hme/list` request in Developer Tools.

Open:

```text
Headers
→ Request Headers
→ Cookie
```

Copy the value after `Cookie:`.

Example format:

```text
X-APPLE-WEBAUTH-USER=...; X-APPLE-WEBAUTH-TOKEN=...; ...
```

Do not include:

```text
Cookie:
```

Only copy the cookie value itself.

### Alternative method

In Chrome or Edge:

1. Right-click the `/v2/hme/list` request.
2. Select **Copy → Copy as cURL**.
3. Paste it into a text editor.
4. Find the section similar to:

```text
-H 'cookie: ...'
```

5. Copy only the cookie value.

## Security Warning

Your iCloud Cookie Header String is sensitive authentication information.

Never:

* post it on GitHub
* send it to another person
* include it in a bug report
* include it in screenshots
* publish a Shortcut containing your cookie

The shortcuts store the cookie locally for API requests.

No external server is required.

## 5. Run HME Setup

Run **HME Setup**.

You will be asked:

```text
Enter your iCloud Hide My Email /v2/hme/list request URL.
```

Paste the complete `/v2/hme/list` Request URL.

Next:

```text
Paste your iCloud Cookie Header String.
Do not include the "Cookie:" prefix.
```

Paste your Cookie Header String.

HME Setup will use `/v2/hme/list` to verify that the session is valid.

If successful, the shortcut stores the configuration in:

```text
iCloud Drive
└── Shortcuts
    └── HME
        ├── hme_list_url.txt
        └── icloud_cookie.txt
```

You should see:

```text
Setup Complete

Your iCloud session was verified successfully.
HME Generate is ready to use.
```

## Using HME Generate

Run **HME Generate**.

The shortcut will:

1. Load your saved HME configuration.
2. Convert the saved `/v2/hme/list` URL into the `/v1/hme/generate` endpoint.
3. Request a new Hide My Email address.
4. Ask you for a label.
5. Reserve the address using `/v1/hme/reserve`.
6. Copy the resulting address to your clipboard.
7. Display the generated address.

You will be prompted with:

```text
Enter a label for this Hide My Email address.
```

Example:

```text
Amazon
```

or:

```text
Newsletter
```

After the request succeeds, the address is copied automatically.

Example:

```text
Hide My Email Created

example@icloud.com

The address has been copied to your clipboard.
```

## Updating an Expired Cookie

iCloud web session cookies eventually expire.

You do not need to repeat the full setup unless the saved request URL also stops working.

Obtain a new Cookie Header String from iCloud.com using the same process described above.

Then run:

**HME Update Cookie**

You will be asked:

```text
Paste your new iCloud Cookie Header String.
Do not include the "Cookie:" prefix.
```

The shortcut will:

1. Load the existing `hme_list_url.txt`.
2. Test the new cookie using `/v2/hme/list`.
3. Replace `icloud_cookie.txt` only if authentication succeeds.

Your saved HME URL remains unchanged.

## Troubleshooting

### iCloud authentication failed

If you see:

```text
iCloud authentication failed.
```

Your cookie may be expired or incomplete.

Obtain a new Cookie Header String and run **HME Update Cookie**.

### Invalid Hide My Email request URL

Make sure you copied the complete request whose path contains:

```text
/v2/hme/list
```

The hostname should normally look similar to:

```text
pXXX-maildomainws.icloud.com
```

### Missing X-APPLE-WEBAUTH-USER

If the request reports:

```text
Missing X-APPLE-WEBAUTH-USER cookie
```

your Cookie Header String is incomplete.

Copy the full Cookie header from the actual `/v2/hme/list` request.

### Generate works but Reserve fails

The Generate and Reserve endpoints perform different operations.

The shortcut uses:

```text
GET /v2/hme/list
```

for validation,

```text
POST /v1/hme/generate
```

for address generation,

and:

```text
POST /v1/hme/reserve
```

for reserving the address.

Make sure you have not manually replaced the saved HME URL with a Generate or Reserve URL.

## How It Works

iCloud.com uses internal web APIs for Hide My Email.

This project reuses the authenticated iCloud web session to access those endpoints.

The stored `/v2/hme/list` URL contains the account-specific mail service host and request parameters.

HME Generate derives the required API endpoints from that URL:

```text
/v2/hme/list
      ↓
/v1/hme/generate
```

and:

```text
/v2/hme/list
      ↓
/v1/hme/reserve
```

No Apple ID password is stored or transmitted by the shortcuts.

## Privacy

The shortcuts do not require an external server.

Your:

* iCloud Cookie Header String
* HME request URL
* generated Hide My Email addresses

are handled locally by Apple Shortcuts and your iCloud Drive configuration files.

You are responsible for protecting your iCloud session data.

## Disclaimer

This project is unofficial and is not affiliated with, endorsed by, or supported by Apple Inc.

It relies on undocumented internal iCloud.com APIs.

Apple may change these APIs, request parameters, authentication requirements, or endpoint behavior at any time, which may cause the shortcuts to stop working.

Use at your own risk.

"Apple", "iCloud", "iCloud+", "Hide My Email", "iPhone", and "Shortcuts" are trademarks of Apple Inc.
