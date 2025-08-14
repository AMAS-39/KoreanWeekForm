# Korea Week Registration Form

A beautiful, modern registration form for Korea Week at Tishk International University. Built with HTML, Tailwind CSS, and vanilla JavaScript.

## Features

- 🎥 **Hero Section**: Full-width video background with Korean cultural content
- 📱 **Responsive Design**: Works perfectly on desktop, tablet, and mobile
- 🔄 **Multi-step Form**: 5-step registration process with progress indicator
- ✅ **Form Validation**: Client-side validation with error handling
- 🎨 **Modern UI**: Clean, airy design with smooth animations
- ♿ **Accessible**: Proper ARIA labels, keyboard navigation, and focus states
- 🛡️ **Spam Protection**: Honeypot field and form validation
- 📊 **Capacity Management**: Dynamic activity capacity display

## Form Sections

1. **Personal Information**: Name, age, email, phone, city
2. **Day Selection**: Choose between August 29 or 30
3. **Activity Selection**: Kimbap-making, Taekwondo, Korean makeup, Squid Game
4. **Additional Details**: Dietary restrictions, emergency contact, media consent, waiver
5. **Review & Submit**: Final review before submission

## Deployment Options

### Option A: Netlify Forms + Zapier/Make (Recommended)

1. **Deploy to Netlify**:
   - Drag and drop the `index.html` file to [Netlify](https://netlify.com)
   - Or connect your GitHub repository
   - The form will automatically work with Netlify Forms

2. **Set up Zapier/Make Integration**:
   - Create a new Zap/Make scenario
   - Trigger: Netlify Forms (new submission)
   - Action: Google Sheets (add row)
   - Map form fields to spreadsheet columns

3. **Configure Google Sheets**:
   - Create a new Google Sheet
   - Set up columns for all form fields
   - Share with Zapier/Make integration

### Option B: Vercel Serverless Function

1. **Deploy to Vercel**:
   ```bash
   npm install -g vercel
   vercel
   ```

2. **Create API Route**:
   Create `api/submit.js`:
   ```javascript
   import { google } from 'googleapis';

   export default async function handler(req, res) {
     if (req.method !== 'POST') {
       return res.status(405).json({ message: 'Method not allowed' });
     }

     try {
       // Google Sheets API setup
       const auth = new google.auth.GoogleAuth({
         credentials: JSON.parse(process.env.GOOGLE_SERVICE_ACCOUNT_KEY),
         scopes: ['https://www.googleapis.com/auth/spreadsheets'],
       });

       const sheets = google.sheets({ version: 'v4', auth });
       
       // Add row to Google Sheet
       await sheets.spreadsheets.values.append({
         spreadsheetId: process.env.GOOGLE_SHEET_ID,
         range: 'Sheet1!A:Z',
         valueInputOption: 'RAW',
         requestBody: {
           values: [[
             new Date().toISOString(),
             req.body.fullName,
             req.body.age,
             req.body.email,
             req.body.phone,
             req.body.city,
             req.body.selectedDay,
             req.body.activities?.join(', '),
             req.body.emergencyName,
             req.body.emergencyPhone,
             req.body.emergencyRelation,
             req.body.mediaConsent,
             req.body.dietary,
             req.body.comments
           ]]
         }
       });

       res.status(200).json({ message: 'Registration successful' });
     } catch (error) {
       console.error('Error:', error);
       res.status(500).json({ message: 'Registration failed' });
     }
   }
   ```

3. **Environment Variables**:
   - `GOOGLE_SERVICE_ACCOUNT_KEY`: JSON key from Google Cloud Console
   - `GOOGLE_SHEET_ID`: Your Google Sheet ID

### Option C: Google Apps Script

1. **Create Google Apps Script**:
   ```javascript
   function doPost(e) {
     try {
       const data = JSON.parse(e.postData.contents);
       const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
       
       sheet.appendRow([
         new Date(),
         data.fullName,
         data.age,
         data.email,
         data.phone,
         data.city,
         data.selectedDay,
         data.activities?.join(', '),
         data.emergencyName,
         data.emergencyPhone,
         data.emergencyRelation,
         data.mediaConsent,
         data.dietary,
         data.comments
       ]);
       
       return ContentService.createTextOutput(JSON.stringify({
         success: true,
         message: 'Registration successful'
       })).setMimeType(ContentService.MimeType.JSON);
     } catch (error) {
       return ContentService.createTextOutput(JSON.stringify({
         success: false,
         message: 'Registration failed'
       })).setMimeType(ContentService.MimeType.JSON);
     }
   }
   ```

2. **Deploy as Web App**:
   - Set access to "Anyone"
   - Copy the web app URL
   - Update form action in `index.html`

## Configuration

### Capacity Management

Edit the `capacityConfig` object in the JavaScript to update activity capacities:

```javascript
const capacityConfig = {
  "day1": {
    "kimbap": 30,
    "taekwondo": 25,
    "makeup": 20,
    "squid": 40
  },
  "day2": {
    "kimbap": 30,
    "taekwondo": 25,
    "makeup": 20,
    "squid": 40
  }
};
```

### Form Endpoint

Update the form submission endpoint in the JavaScript:

```javascript
// Replace the setTimeout with actual fetch call
fetch('/api/submit', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify(formData)
})
.then(response => response.json())
.then(data => {
  if (data.success) {
    showSuccessModal();
  } else {
    alert('Registration failed. Please try again.');
  }
});
```

## Customization

### Colors
The form uses a red color scheme (`red-600`, `red-700`). To change colors:
1. Update all `red-` classes to your preferred color
2. Update the Tailwind config if using custom colors

### Video Background
Replace the video source URL in the HTML:
```html
<source src="YOUR_VIDEO_URL" type="video/mp4">
```

### Form Fields
Add or remove fields by:
1. Updating the form section functions
2. Adding validation logic
3. Updating the review section
4. Modifying the backend integration

## Browser Support

- Chrome 60+
- Firefox 55+
- Safari 12+
- Edge 79+

## Performance

- Lazy loading for images
- Optimized video with poster fallback
- Minimal JavaScript bundle
- CDN-hosted Tailwind CSS

## Security

- Honeypot field for spam protection
- Client-side validation
- HTTPS required for form submission
- No sensitive data stored in client-side code

## Support

For issues or questions:
1. Check the browser console for errors
2. Verify form endpoint configuration
3. Test with different browsers
4. Check network connectivity

## License

This project is open source and available under the MIT License.
