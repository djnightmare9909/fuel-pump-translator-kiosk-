# Retail Communication Kiosk for Fuel Sales

This document outlines the design, deployment, and configuration of the counter-mounted retail kiosk application. The interface is designed exclusively to resolve language and communication barriers between retail counter clerks and customers prepaying for fuel.

By providing a highly visible, high-contrast, touch-optimized workflow, the kiosk enables users to independently specify their language preference, pump number, transaction amount, and desired fuel grade.

## Purpose and Scope

In retail environments, verbal communication barriers regarding numerical values (such as pump numbers and fuel amounts) often lead to transaction errors, incorrect pump authorizations, and slow service throughput. This kiosk acts as a translation and validation bridge. 

The application is offline-first, requiring no external network requests, third-party libraries, or web-loaded fonts. This guarantees operational uptime even during store network disruptions.

## Key Interface Specifications

To accommodate a wide demographic of users, including those with visual impairments or motor skill difficulties:

* **Touch Targets**: All interactive elements maintain a minimum target size of 60 pixels to prevent selection misses.
* **Typography**: Primary instructional labels utilize a minimum sizing of 24 pixels for immediate legibility from typical counter-mounted distances.
* **Layout Adaptability**: The interface handles responsive viewport modifications, adjusting dynamically to portrait tablets and landscape-mounted counter units without modifying vertical scroll behavior.
* **Keyboard Management**: The numeric inputs use dedicated custom visual keypads. This prevents the operating system virtual keyboard from triggering and overlapping critical instructional regions.

## Interactive Protocol Flow

The system runs on a rigid, sequential state machine to ensure clean progress tracking:

1. **Language Selection**: The user selects their preferred language from a localized terminal screen. The interface translates all following screens immediately. Supported default locales include English, Spanish, Brazilian Portuguese, Haitian Creole, and Jamaican Patois.
2. **Pump Selection**: A visual numeric pad limits pump inputs strictly to numbers 1 through 12. If a user inputs an out-of-bounds number, the system displays a clear warning and disables progression.
3. **Transaction amount**: Users can choose standard dollar preset values directly ($10, $20, $30, $40, $60, or $100) or choose a custom dollar entry path controlled by a secondary numeric keypad.
4. **Grade Selection**: The user selects from three physical grade buckets, regular, midgrade, or premium. Daily price per gallon displays have been omitted in this version to eliminate maintenance overhead for store staff.
5. **Clerk Summary and Customer Visual Instructions**:
   - The customer sees a high-contrast validation screen instructing them to walk to their designated pump and press the color-coded grade button on the dispenser.
   - A custom interactive inline SVG highlights and circles the exact button chosen on the physical pump layout.
   - A restricted, low-contrast box at the bottom provides a summary written in English specifically for the store clerk to reference before executing the system authorization on the point of sale console.

## Technical Details

The application is encapsulated entirely in a single source index.html document. 

### Data Structure and Localization Matrix

All screen prompts, validation warns, button labels, and final instructions are managed from a centralized localization matrix declared within the client script. Adding a new regional dialect or language translation is handled by expanding this dictionary structure with corresponding keys:

```javascript
const translations = {
  en: {
    back: "← Back",
    confirm: "Confirm",
    step1Title: "Language Select",
    step2Title: "Enter Pump Number (1 - 12)",
    step2Error: "Please enter a pump number between 1 and 12.",
    // additional language nodes...
  }
};
```

### SVG Path and Grade Highlighting

The checkout screen dynamically triggers pulse rings and coordinate arrow icons embedded directly inside the physical pump SVG rendering layer. The state machine modifies the opacity and active class states based on the selection made in Step 4.

## Hardware Integration and Deployment

* **Hardware Recommendation**: Designed for 10-inch or 12-inch tablets secured in locked table-top kiosk mounts at the final checkout register counter.
* **Local Caching (Offline Mode)**: Because the file comprises entirely inline styling and scripts with zero external asset references (such as remote icon catalogs or external scripts), the file can be saved locally on the device file-system and loaded via a browser bookmark or set up as a standard Chromium Progressive Web Application.
