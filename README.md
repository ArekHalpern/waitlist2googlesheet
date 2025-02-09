# Waitlist Collection App

A simple and modern waitlist collection application built with Next.js 14, featuring Google Sheets integration for easy email management.

## Features

- Clean, modern UI with Tailwind CSS and shadcn/ui
- Email validation and spam protection
- Google Sheets integration for email storage
- Toast notifications for user feedback
- Rate limiting to prevent abuse
- Human-readable timestamps in ET

## Prerequisites

- Node.js 18.17 or later
- A Google Cloud account
- A Google Sheet to store emails

## Setup Instructions

### 1. Local Development Setup

```bash
# Clone the repository
git clone <your-repo-url>
cd waitlist

# Install dependencies
pnpm install

# Copy the environment variables file
cp .env.example .env
```

### 2. Google Cloud Setup

1. Create a Google Cloud Project:

   - Go to [Google Cloud Console](https://console.cloud.google.com)
   - Click "New Project" and give it a name
   - Note down your Project ID

2. Enable the Google Sheets API:

   - In your project, go to "APIs & Services" > "Library"
   - Search for "Google Sheets API"
   - Click "Enable"

3. Create Service Account Credentials:

   - Go to "APIs & Services" > "Credentials"
   - Click "Create Credentials" > "Service Account"
   - Fill in the service account details
   - Click "Create and Continue"
   - Skip role assignment (or assign "Editor" if you prefer)
   - Click "Done"

4. Generate Service Account Key:
   - Find your service account in the credentials list
   - Click on the service account email
   - Go to "Keys" tab
   - Click "Add Key" > "Create New Key"
   - Choose JSON format
   - The key file will download automatically
   - Save this file as `service-account.json` in your project root

### 3. Google Sheet Setup

1. Create a new Google Sheet:

   - Go to [Google Sheets](https://sheets.google.com)
   - Create a new blank spreadsheet
   - Add headers in the first row: "email", "timestamp"
   - Copy the Sheet ID from the URL (the long string between /d/ and /edit)
     Example: `https://docs.google.com/spreadsheets/d/`**THIS-IS-YOUR-SHEET-ID**`/edit`

2. Share with Service Account:
   - Click "Share" in your Google Sheet
   - Add the service account email (found in your service-account.json file)
   - Give "Editor" access
   - Uncheck "Notify people"
   - Click "Share"

### 4. Environment Variables

Update your `.env` file with the following values:

```env
# Google Sheets Configuration
GOOGLE_SHEET_ID=your-sheet-id-here

# For local development, you can use the path to the JSON file
GOOGLE_APPLICATION_CREDENTIALS=service-account.json

# For production, use the entire JSON content (recommended)
# GOOGLE_APPLICATION_CREDENTIALS={"type":"service_account","project_id":"..."}

# Rate Limiting Configuration
RATE_LIMIT_REQUESTS=5        # Maximum requests per IP per duration
RATE_LIMIT_DURATION=3600000  # Duration in milliseconds (1 hour = 3600000)
```

### 5. Running the Application

```bash
# Start the development server
pnpm dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the application.

## Development

- The main page is in `app/page.tsx`
- The form component is in `components/forms/WaitlistForm.tsx`
- The API endpoint is in `app/api/waitlist/route.ts`

## Deployment

### Vercel Deployment

1. Push your code to GitHub
2. Import your repository in Vercel
3. Add environment variables in Vercel:
   - Add `GOOGLE_SHEET_ID` as is
   - For `GOOGLE_APPLICATION_CREDENTIALS`:
     - Copy the ENTIRE contents of your `service-account.json` file
     - Paste it directly as the value for `GOOGLE_APPLICATION_CREDENTIALS`
     - Make sure to include all JSON fields and format it as a single line
     - Example format:
       ```
       GOOGLE_APPLICATION_CREDENTIALS={"type":"service_account","project_id":"your-project","private_key_id":"...","private_key":"-----BEGIN PRIVATE KEY-----\n..."}
       ```
4. Deploy!

### Other Deployment Platforms

For other platforms, ensure you:

1. Set up all environment variables as described above
2. Use the JSON content directly in `GOOGLE_APPLICATION_CREDENTIALS`
3. Verify the service account has access to your Google Sheet

## Environment Variables for Production

When deploying to production:

1. Required variables:

   - `GOOGLE_SHEET_ID`: Your Google Sheet ID
   - `GOOGLE_APPLICATION_CREDENTIALS`: The entire JSON contents of your service account credentials

2. Optional variables:
   - `RATE_LIMIT_REQUESTS`: Maximum requests per IP (default: 5)
   - `RATE_LIMIT_DURATION`: Duration in milliseconds (default: 3600000)

## Troubleshooting

Common issues and solutions:

1. **Google Sheets API Error**:

   - Verify the API is enabled in Google Cloud Console
   - Check that your service account has editor access to the sheet
   - Ensure environment variables are properly formatted
   - Verify the service account JSON is valid

2. **Service Account Credentials Format**:

   - The `GOOGLE_APPLICATION_CREDENTIALS` must be a single-line JSON string
   - Common error: "Invalid service account credentials format"
   - Solution:
     - Remove all newlines and extra spaces from the JSON
     - Keep `\n` characters in the private key
     - Ensure all quotes are properly escaped
     - Example format:
       ```env
       GOOGLE_APPLICATION_CREDENTIALS={"type":"service_account","project_id":"your-project","private_key_id":"...","private_key":"-----BEGIN PRIVATE KEY-----\nMIIE...==\n-----END PRIVATE KEY-----\n","client_email":"..."}
       ```
     - If using a local file in development, use `GOOGLE_APPLICATION_CREDENTIALS=service-account.json`

3. **ENAMETOOLONG Error**:

   - Error message: "Error: ENAMETOOLONG: name too long, open '/var/task/frontend/{...}'"
   - This occurs when the system tries to treat the JSON string as a file path
   - Solution:
     - Update the code to parse the credentials directly from the environment variable
     - Do not use `fs.readFileSync` with the credentials string
     - Make sure your deployment platform supports long environment variable values
     - For Vercel: Use the environment variables UI or CLI to set the value
     - For other platforms: Check their documentation for maximum environment variable length limits

4. **Rate Limiting Issues**:

   - Default limit is 5 requests per hour per IP
   - Adjust `RATE_LIMIT_REQUESTS` and `RATE_LIMIT_DURATION` if needed

5. **Invalid Email Formats**:

   - The app validates email format before submission
   - Check the email validation regex in the API route if needed

6. **Service Account Issues**:
   - Ensure the service account email has Editor access to the sheet
   - Verify the JSON file is properly formatted
   - Check that all required fields are present in the service account JSON

## Learn More

- [Next.js Documentation](https://nextjs.org/docs)
- [Google Sheets API Documentation](https://developers.google.com/sheets/api)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [shadcn/ui Documentation](https://ui.shadcn.com)
