[DoctorAmp-User-Guide.md](https://github.com/user-attachments/files/23605828/DoctorAmp-User-Guide.md)
# DoctorAmp Demo - User Guide
**Amplitude Browser SDK 2.0 Integration with GDPR/CNIL Compliance**

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Demo Features](#demo-features)
3. [Amplitude SDK Configuration](#amplitude-sdk-configuration)
4. [GDPR & CNIL Compliance](#gdpr--cnil-compliance)
5. [Session Replay Setup](#session-replay-setup)
6. [Privacy Settings](#privacy-settings)
7. [Event Tracking Strategy](#event-tracking-strategy)
8. [Testing & Validation](#testing--validation)
   - [Heatmaps Analysis with Hash-Based Routing](#5-heatmaps-analysis-with-hash-based-routing)
9. [Production Checklist](#production-checklist)

---

## Overview

**DoctorAmp** is a healthcare-inspired medical appointment booking platform built to demonstrate Amplitude Browser SDK 2.0 capabilities with full GDPR and French CNIL compliance.

### Key Technologies
- **Amplitude Browser SDK**: v2.11.2
- **Session Replay Plugin**: v1.20.1
- **API Key**: Available in your Amplitude Project Settings
- **Files**: 
  - `doctoraamp-demo.html` - Standard version
  - `doctoraamp-demo-heatmaps.html` - Enhanced with hash-based routing for Heatmaps

---

## Demo Features

### 🏥 Healthcare Platform Features
- **Homepage** with hero search and specialty browsing
- **Doctor Listings** with filters (availability, consultation type, rating, languages)
- **Sample Doctors**: 5 doctors (standard version) or 19 doctors across 8 specialties (heatmaps version)
- **3-Step Booking Flow**:
  1. Select appointment time
  2. Enter patient details
  3. Booking confirmation
- **Multi-Step Authentication**:
  1. Choose Login or Sign Up
  2. Enter email address
  3. Enter password (login) or complete registration (signup)
- **User Profile** with dropdown menu

### 📊 Amplitude Analytics Features
- Session Replay (100% sample rate for demo)
- Selective Autocapture (Page Views, Attribution, Sessions only)
- Manual event tracking for business-critical actions
- GDPR-compliant cookie consent modal
- Email hashing for User ID privacy

---

## Amplitude SDK Configuration

### SDK Initialization

The SDK is configured using **manual loading** to ensure GDPR compliance:

```javascript
// Session Replay Plugin
window.amplitude.add(window.sessionReplay.plugin({sampleRate: 1}));

// SDK Initialization
window.amplitude.init('AMPLITUDE_PROJECT_API_KEY', {
    fetchRemoteConfig: true,
    autocapture: {
        attribution: true,           // ✅ UTM parameters, referrers, campaigns
        pageViews: true,              // ✅ Automatic page view tracking
        sessions: true,               // ✅ Session start/end events
        elementInteractions: false,   // ❌ Disabled for cleaner analytics
        formInteractions: false,      // ❌ Disabled
        fileDownloads: false          // ❌ Disabled
    }
});
```

### Why This Configuration?

**Manual Loading vs Script Loader**
- ❌ **Script Loader** (`cdn.amplitude.com/script/API_KEY.js`) auto-initializes before consent
- ✅ **Manual Loading** (`cdn.amplitude.com/libs/analytics-browser-...`) gives full control

**Selective Autocapture**
- Only tracks essential events automatically
- Reduces noise in analytics
- Focuses on business-critical metrics

**Remote Configuration**
- `fetchRemoteConfig: true` enables server-side control
- Configure autocapture settings from Amplitude UI
- No code changes needed for adjustments

📖 **Reference**: [Amplitude Browser SDK 2.0 Documentation](https://amplitude.com/docs/sdks/analytics/browser/browser-sdk-2)

---

## GDPR & CNIL Compliance

### French CNIL Requirements

The **CNIL (Commission Nationale de l'Informatique et des Libertés)** is France's data protection authority. DoctorAmp follows CNIL guidelines for cookie consent.

### Implementation Approach

#### 1. **Cookie Consent Modal**

**Features:**
- Centered modal overlay with blur effect on background
- French language: "Nous respectons la vie privée de nos utilisateurs"
- Three clear options:
  - **EN SAVOIR PLUS** (Learn More)
  - **REFUSER** (Refuse)
  - **ACCEPTER** (Accept)
- Links to Cookie Policy and Privacy Policy

**User Experience:**
- Modal appears on first visit
- Background content is blurred but modal is crystal clear
- User cannot access site until making a choice
- Choice is stored in `localStorage` (`cookie_consent`)

#### 2. **SDK Initialization Only After Consent**

```javascript
function acceptCookies() {
    localStorage.setItem('cookie_consent', 'accepted');
    // Remove blur and modal
    document.getElementById('cookieOverlay').classList.remove('active');
    document.getElementById('mainContent').classList.remove('blur-content');
    
    // ✅ ONLY NOW initialize Amplitude
    initializeAmplitude();
}
```

**Critical Points:**
- ✅ SDK scripts are **loaded** but not **initialized** until consent
- ✅ No tracking occurs before explicit user consent
- ✅ If user clicks "REFUSER", SDK never initializes
- ✅ Consent persists across sessions via `localStorage`

#### 3. **Amplitude Cookies Used**

When consent is given, Amplitude stores these cookies:

| Cookie Name | Purpose | Expiration | CNIL Classification |
|------------|---------|------------|---------------------|
| `AMP_*` | Device ID, Session ID | 1 year | Analytics (requires consent) |
| `AMP_MKTG_*` | Attribution data (UTM params) | 1 year | Marketing (requires consent) |

📖 **Reference**: [Amplitude Cookies Documentation](https://amplitude.com/docs/sdks/analytics/browser/cookies-and-consent-management#amplitude-cookies)

#### 4. **CNIL Compliance Checklist**

✅ **Prior consent obtained** before any tracking  
✅ **Clear information** provided about cookie usage  
✅ **Easy to refuse** (REFUSER button equally prominent)  
✅ **Granular choice** (links to detailed policies)  
✅ **Consent storage** (localStorage for 365 days max)  
✅ **Proof of consent** (timestamp can be added if needed)

📖 **Reference**: [CNIL France - FAQ](https://amplitude.com/docs/sdks/analytics/browser/cookies-and-consent-management#cnil-france---frequently-asked-questions)

---

## Session Replay Setup

### Configuration

```javascript
window.amplitude.add(window.sessionReplay.plugin({
    sampleRate: 1  // 100% for demo (use 0.1-0.3 in production)
}));
```

### What Session Replay Captures

✅ **User Interactions**: Clicks, scrolls, hovers, form interactions  
✅ **Page Navigation**: SPA route changes, page views  
✅ **Network Activity**: API calls, errors  
✅ **Console Logs**: JavaScript errors and warnings  
✅ **DOM Changes**: Dynamic content updates  

### Privacy by Default

Amplitude Session Replay has built-in privacy protections:

- **Text Masking**: Sensitive text is automatically masked
- **Input Masking**: All password fields are masked
- **Image Blocking**: External images can be blocked
- **Privacy API**: Additional controls available

📖 **Reference**: [Session Replay Documentation](https://amplitude.com/docs/session-replay)

---

## Privacy Settings

### Default Privacy Configuration

Session Replay in DoctorAmp uses Amplitude's default privacy settings:

```javascript
// Default privacy (automatically applied)
{
    maskAllInputs: true,      // All input fields masked
    maskAllText: false,       // Text visible (adjust as needed)
    blockClass: 'amp-block',  // Class to block elements
    maskTextClass: 'amp-mask' // Class to mask text
}
```

### Customizing Privacy Settings

For enhanced privacy, add this configuration:

```javascript
window.amplitude.add(window.sessionReplay.plugin({
    sampleRate: 1,
    privacyConfig: {
        maskAllInputs: true,     // Mask all form inputs
        maskAllText: true,       // Mask ALL text content
        blockClass: 'amp-block', // Block elements with this class
        ignoreClass: 'amp-ignore' // Don't capture these elements
    }
}));
```

### GDPR-Recommended Settings

For maximum GDPR compliance:

1. **Mask Personal Data**: Use `maskAllInputs: true`
2. **Block Sensitive Sections**: Add `class="amp-block"` to sensitive areas
3. **Sample Rate**: Use 10-30% in production (`sampleRate: 0.1` to `0.3`)
4. **User Consent**: Only enable after explicit consent (✅ already implemented)

### Best Practices for User Consent

According to Amplitude's best practices:

- ✅ **Inform users** about session recording in privacy policy
- ✅ **Obtain consent** before initializing (✅ DoctorAmp does this)
- ✅ **Provide opt-out** option (can be added to user profile settings)
- ✅ **Respect "Do Not Track"** browser settings (optional enhancement)

📖 **References**:
- [Privacy Settings for Session Replay](https://amplitude.com/docs/session-replay/manage-privacy-settings-for-session-replay)
- [Best Practices for User Consent](https://amplitude.com/docs/session-replay/best-practices-for-managing-user-consent)

---

## Event Tracking Strategy

### Automatic Events (Autocapture)

| Event Type | Description | Configuration |
|-----------|-------------|---------------|
| `[Amplitude] Page Viewed` | Automatic page view tracking | `pageViews: true` |
| `session_start` | Session begins | `sessions: true` |
| `session_end` | Session ends | `sessions: true` |
| Attribution Events | UTM parameters, referrer data | `attribution: true` |

### Manual Events Tracked

#### Search & Discovery
- `Search Tab Changed` - User switches between Doctor/Dentist/Specialist
- `Specialty Selected` - User clicks specialty card
- `Search Performed` - User submits search form
- `Filter Changed` - User toggles filters on listings page
- `Doctor Card Clicked` - User clicks on doctor profile

#### Booking Funnel
- `Booking Modal Opened` - User clicks time slot
- `Time Slot Selected` - User chooses appointment time
- `Booking Step Completed` - User completes each step
- **`Appointment Booked`** - 🎯 **Conversion Event**

#### Authentication Funnel
- `Login Page Viewed` - Modal opens
- `Login Started` / `Signup Started` - User chooses flow
- `Email Entered` - Step 2 completed
- `Forgot Password Clicked` - Password recovery
- **`Login Completed`** / **`Signup Completed`** - 🎯 **Conversion Events**

#### User Identity
- `setUserId()` - SHA-256 hashed email for privacy
- User Properties: `email`, `first_name`, `last_name`, `user_type: "patient"`

### Email Hashing for Privacy

```javascript
async function hashEmail(email) {
    const encoder = new TextEncoder();
    const data = encoder.encode(email.toLowerCase());
    const hashBuffer = await crypto.subtle.digest('SHA-256', data);
    const hashArray = Array.from(new Uint8Array(hashBuffer));
    const hashHex = hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
    return hashHex.substring(0, 16); // First 16 characters
}

// Example:
// Email: giuliano.giannini@amplitude.com
// Hashed User ID: a1b2c3d4e5f6g7h8
```

**Why Hash Email?**
- ✅ GDPR compliance (pseudonymization)
- ✅ Consistent user tracking across sessions
- ✅ Email privacy maintained
- ✅ Reversible only with original email (not a security risk)

---

## Testing & Validation

### 1. Cookie Consent Testing

**Test Steps:**
1. Clear `localStorage`: `localStorage.clear()` in console
2. Reload page
3. Verify cookie modal appears
4. Verify background is blurred
5. Click "ACCEPTER"
6. Verify modal disappears
7. Check console for: `✅ Amplitude Analytics initialized`

**Console Validation:**
```javascript
// Check consent status
localStorage.getItem('cookie_consent') // Should return 'accepted'

// Check if SDK is initialized
window.amplitude // Should be defined
```

### 2. Session Replay Validation

Use Amplitude's **Ingestion Monitor** to validate Session Replay:

**Steps:**
1. Go to Amplitude → **Data** → **Session Replay** → **Ingestion Monitor**
2. Verify all 4 checks are ✅ Green:
   - **Remote Configuration**
   - **Analytics Event Data**
   - **Session Replay Ingestion**
   - **Session Replay Data Connection**

**Expected Results:**
- Replays appear within 1-2 minutes
- Country data visible (e.g., "United Kingdom")
- Events linked to replay timeline
- Clicking event jumps to that moment in replay

📖 **Reference**: [Session Replay Ingestion Monitor](https://amplitude.com/docs/session-replay/ingestion-monitor)

### 3. Event Tracking Validation

**Amplitude Debugger:**
1. Go to Amplitude → **Data** → **Sources** → Click your source
2. Open **Event Stream** or **Debugger**
3. Perform actions in DoctorAmp
4. Verify events appear in real-time

**Key Events to Test:**
- ✅ `[Amplitude] Page Viewed` (automatic)
- ✅ `session_start` (automatic)
- ✅ `Search Performed` (manual)
- ✅ `Appointment Booked` (manual)
- ✅ `Login Completed` (manual)

### 4. User Identification Testing

**Test Flow:**
1. Click "Log In" → Complete login flow
2. Enter email: `test.user@example.com`
3. Complete login
4. Check in Amplitude Debugger:
   - User ID should be hashed (e.g., `a1b2c3d4e5f6g7h8`)
   - User Properties should include:
     - `email: "test.user@example.com"`
     - `first_name: "Test"`
     - `last_name: "User"`
     - `user_type: "patient"`

### 5. Heatmaps Analysis with Hash-Based Routing

#### Two Demo Versions Available

**Standard Version**: `doctoraamp-demo.html`
- Single homepage and doctor listings page
- Suitable for basic Session Replay testing

**Heatmaps Version**: `doctoraamp-demo-heatmaps.html` ✨
- **Enhanced with hash-based routing** for specialty pages
- Each specialty has its own unique URL
- **Recommended for Heatmap analysis**

#### How Hash-Based Routing Works

When users click on a specialty card (e.g., "Ophthalmologist"), the URL changes to include a hash:

```
Home:                → doctoraamp-demo-heatmaps.html
Ophthalmologist:     → doctoraamp-demo-heatmaps.html#specialty/ophthalmologist
Gynecologist:        → doctoraamp-demo-heatmaps.html#specialty/gynecologist
Psychologist:        → doctoraamp-demo-heatmaps.html#specialty/psychologist
Cardiologist:        → doctoraamp-demo-heatmaps.html#specialty/cardiologist
Dermatologist:       → doctoraamp-demo-heatmaps.html#specialty/dermatologist
Pediatrician:        → doctoraamp-demo-heatmaps.html#specialty/pediatrician
Dentist:             → doctoraamp-demo-heatmaps.html#specialty/dentist
General Practitioner → doctoraamp-demo-heatmaps.html#specialty/general-practitioner
```

#### Benefits for Heatmap Analysis

**Separate Heatmaps Per Specialty:**
- Each specialty page has its own unique URL
- Amplitude can create individual heatmaps for each page
- Compare user behavior across different specialties

**Enhanced Insights:**
- 🔍 **Click Heatmaps**: Which doctors get clicked most per specialty?
- 📊 **Engagement Patterns**: Do users interact differently on Psychology vs Cardiology pages?
- 🎯 **Conversion Analysis**: Which specialties have highest booking rates?
- 🔄 **Rage Click Detection**: Identify frustration points on specific specialty pages
- 📈 **Scroll Depth**: How far do users scroll on each specialty listing?

#### Technical Implementation

**Doctor Data:**
- 19 doctors across 8 specialties (2-3 per specialty)
- Specialty-specific names, addresses, and badges
- Each specialty filtered automatically when navigating

**Routing Features:**
- ✅ Browser back/forward buttons work
- ✅ Can bookmark specific specialty pages
- ✅ Page title updates dynamically
- ✅ Clean URLs without page reloads (SPA)
- ✅ All analytics continue working

**Page View Tracking:**
```javascript
window.amplitude.track('Page Viewed', {
    page_name: 'Ophthalmologist Listings',
    page_path: '/doctoraamp-demo-heatmaps.html#specialty/ophthalmologist',
    page_title: 'Ophthalmologist in London - DoctorAmp',
    specialty: 'Ophthalmologist'
});
```

#### When to Use Each Version

| Use Case | File to Use |
|----------|-------------|
| Session Replay testing | Either version works |
| Basic event tracking | `doctoraamp-demo.html` (simpler) |
| **Heatmaps analysis** | **`doctoraamp-demo-heatmaps.html`** ✅ |
| A/B testing different specialties | `doctoraamp-demo-heatmaps.html` |
| Comparing conversion by specialty | `doctoraamp-demo-heatmaps.html` |

#### Heatmaps Best Practices

When using Amplitude Heatmaps:

1. **Generate Traffic**: Have multiple users visit each specialty page
2. **Compare Patterns**: Look for differences in click behavior across specialties
3. **Identify Issues**: Find pages with unusual click patterns or rage clicks
4. **Optimize Layout**: Use heatmap data to improve doctor card positioning
5. **Test Changes**: Compare heatmaps before/after UX improvements

📖 **Reference**: [Heatmaps Documentation](https://amplitude.com/docs/session-replay/heatmaps)

---

## Production Checklist

### Before Going Live

#### 1. **Amplitude Configuration**

- [ ] Replace with production API key
- [ ] Adjust Session Replay sample rate: `sampleRate: 0.1` (10%)
- [ ] Review autocapture settings
- [ ] Configure Remote Config in Amplitude UI

#### 2. **Privacy & Compliance**

- [ ] Add real Cookie Policy and Privacy Policy pages
- [ ] Update modal links to point to actual policies
- [ ] Add privacy notice for Session Replay
- [ ] Consider adding opt-out in user profile settings
- [ ] Add cookie consent timestamp tracking (for audit trail)

#### 3. **CNIL Specific Requirements**

- [ ] Ensure consent banner appears on all entry pages
- [ ] Verify consent is requested before ANY tracking
- [ ] Provide granular consent options if tracking multiple vendors
- [ ] Keep records of consent (user ID, timestamp, consent given/refused)
- [ ] Add mechanism to withdraw consent

**CNIL Documentation:**
```javascript
// Example: Add consent timestamp
function acceptCookies() {
    const consentData = {
        status: 'accepted',
        timestamp: new Date().toISOString(),
        version: '1.0' // Policy version
    };
    localStorage.setItem('cookie_consent', JSON.stringify(consentData));
    initializeAmplitude();
}
```

#### 4. **Session Replay Privacy**

- [ ] Review privacy settings for your use case
- [ ] Add `class="amp-block"` to sensitive areas (e.g., medical records)
- [ ] Consider `maskAllText: true` for maximum privacy
- [ ] Test replay in staging to verify masking works
- [ ] Document what is captured in Privacy Policy

#### 5. **Performance**

- [ ] Test page load time with SDK
- [ ] Verify SDK doesn't block page rendering
- [ ] Monitor network requests to Amplitude
- [ ] Check console for errors

#### 6. **Event Tracking**

- [ ] Define conversion events (booking, signup)
- [ ] Set up funnels in Amplitude
- [ ] Create dashboards for key metrics
- [ ] Test all critical user journeys

#### 7. **Documentation**

- [ ] Document event tracking plan
- [ ] Create Amplitude access guide for team
- [ ] Document User ID hashing approach
- [ ] Keep this user guide updated

---

## Additional Resources

### Amplitude Documentation

1. **Browser SDK 2.0**: https://amplitude.com/docs/sdks/analytics/browser/browser-sdk-2
2. **Cookies & Consent Management**: https://amplitude.com/docs/sdks/analytics/browser/cookies-and-consent-management
3. **CNIL France FAQ**: https://amplitude.com/docs/sdks/analytics/browser/cookies-and-consent-management#cnil-france---frequently-asked-questions
4. **Session Replay**: https://amplitude.com/docs/session-replay
5. **Privacy Settings**: https://amplitude.com/docs/session-replay/manage-privacy-settings-for-session-replay
6. **User Consent Best Practices**: https://amplitude.com/docs/session-replay/best-practices-for-managing-user-consent
7. **Ingestion Monitor**: https://amplitude.com/docs/session-replay/ingestion-monitor
8. **Heatmaps**: https://amplitude.com/docs/session-replay/heatmaps

### CNIL Resources

- **CNIL Official Guidelines**: https://www.cnil.fr/en/cookies-and-other-trackers
- **GDPR Compliance**: https://gdpr.eu/cookies/

---

## Support

For questions or issues:

1. **Amplitude Support**: https://help.amplitude.com
2. **Community**: https://community.amplitude.com
3. **CNIL Contact**: https://www.cnil.fr/en/contact-cnil

---

**Version**: 1.1 (Heatmaps Enhanced)  
**Last Updated**: November 2025  
**Demo Files**: 
- `doctoraamp-demo.html` (Standard)
- `doctoraamp-demo-heatmaps.html` (Hash-based routing for Heatmaps)

---

*Built with ❤️ by Giuliano Giannini. Powered by Amplitude Analytics.*
