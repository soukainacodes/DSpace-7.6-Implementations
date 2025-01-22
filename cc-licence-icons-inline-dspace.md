# Guide: Adding Creative Commons Icons in DSpace 7.6 Using Inline HTML

This guide explains how to display Creative Commons (CC) icons dynamically in DSpace 7.6 using inline HTML without modifying `.ts` components. This approach works for all DSpace entities with CC licenses.

---

## Prerequisites

1. Ensure you have access to the DSpace Angular front-end codebase.
2. Verify that the Creative Commons license metadata is configured and populated in your DSpace instance:
   - License URI stored in `dc.rights.uri`.
   - License text stored in `dc.rights`.
3. Download the CC license images from the [DSpace GitHub repository](https://github.com/DSpace/dspace-angular/tree/main/src/assets/images/cc-licenses) and place them in the `src/assets/images/cc-licenses` directory.

---

## Inline HTML Code

Add the following code to your Angular template (e.g., `*.component.html`) where you want the Creative Commons icons to appear:

```html
<div *ngIf="object?.metadata?.['dc.rights.uri']?.[0]?.value as licenseUri">

  <a>{{ 'llicencia.primertext' | translate }}</a>
  <div style="height: 0.5rem;"></div>

  <ng-container [ngSwitch]="licenseUri">

    <!-- BY-NC-ND -->
    <a *ngSwitchCase="'http://creativecommons.org/licenses/by-nc-nd/3.0/es/'" 
       [href]="licenseUri" 
       target="_blank" 
       rel="noopener noreferrer">
      <img src="/assets/images/cc-licenses/by-nc-nd.png" 
           alt="Creative Commons BY-NC-ND License" 
           style="width: 30%;">
    </a>

    <!-- BY-NC -->
    <a *ngSwitchCase="'http://creativecommons.org/licenses/by-nc/3.0/es/'" 
       [href]="licenseUri" 
       target="_blank" 
       rel="noopener noreferrer">
      <img src="/assets/images/cc-licenses/by-nc.png" 
           alt="Creative Commons BY-NC License" 
           style="width: 30%;">
    </a>

    <!-- BY-NC-SA -->
    <a *ngSwitchCase="'http://creativecommons.org/licenses/by-nc-sa/3.0/es/'" 
       [href]="licenseUri" 
       target="_blank" 
       rel="noopener noreferrer">
      <img src="/assets/images/cc-licenses/by-nc-sa.png" 
           alt="Creative Commons BY-NC-SA License" 
           style="width: 30%;">
    </a>

    <!-- BY-ND -->
    <a *ngSwitchCase="'http://creativecommons.org/licenses/by-nd/3.0/es/'" 
       [href]="licenseUri" 
       target="_blank" 
       rel="noopener noreferrer">
      <img src="/assets/images/cc-licenses/by-nd.png" 
           alt="Creative Commons BY-ND License" 
           style="width: 30%;">
    </a>

    <!-- BY -->
    <a *ngSwitchCase="'http://creativecommons.org/licenses/by/3.0/es/'" 
       [href]="licenseUri" 
       target="_blank" 
       rel="noopener noreferrer">
      <img src="/assets/images/cc-licenses/by.png" 
           alt="Creative Commons BY License" 
           style="width: 30%;">
    </a>

    <!-- BY-SA -->
    <a *ngSwitchCase="'http://creativecommons.org/licenses/by-sa/3.0/es/'" 
       [href]="licenseUri" 
       target="_blank" 
       rel="noopener noreferrer">
      <img src="/assets/images/cc-licenses/by-sa.png" 
           alt="Creative Commons BY-SA License" 
           style="width: 30%;">
    </a>

    <!-- Zero -->
    <a *ngSwitchCase="'http://creativecommons.org/publicdomain/zero/1.0/'" 
       [href]="licenseUri" 
       target="_blank" 
       rel="noopener noreferrer">
      <img src="/assets/images/cc-licenses/zero.png" 
           alt="Creative Commons Zero License" 
           style="width: 30%;">
    </a>

  </ng-container>

  <div style="height: 0.5rem;"></div>

  <div *ngIf="object?.metadata?.['dc.rights']?.[0]?.value as licenseText">
    <span>
      {{ 'llicencia.segontext' | translate }}
    </span>
    <span>{{ licenseText }}</span>
  </div>

</div>
```


---

## How to Customize

1. **Add More Licenses**:
   - Extend the `[ngSwitch]` block with additional `*ngSwitchCase` cases for other licenses.

2. **Adjust Styles**:
   - Modify the `style` attribute (e.g., `width`) in `<img>` tags to fit your design requirements.

3. **Translation Keys, in this case it is in Catalan**:
   - Add the following translation keys to your `i18n` JSON or JSON5 files:
     ```json
     {
       "llicencia.primertext": "Creative Commons License:",
       "llicencia.segontext": "License details:"
     }
     ```

