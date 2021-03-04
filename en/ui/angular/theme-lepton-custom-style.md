# Adding Custom Style To Theme Lepton

There is a `customStyle` boolean configuration in `ThemeLeptonModule`'s `forRoot` method. If this configuration is true, the style selection box is not included in the theme settings form and `ThemeLeptonModule` does not load its own styles. In this case, a custom style file must be added to the styles array in `angular.json` or must be imported by `style.scss`.

> Only angular project styles can be changed in this way. If the authorization flow is authorization code flow, MVC pages (login, profile, etc) are not affected by this change.

Custom style implementation can be done with the following steps

Set `customStyle` property to `true` where is `ThemeLeptonModule` imported with `forRoot` method. 
```javascript
// app.module.ts
ThemeLeptonModule.forRoot({
    customStyle: true
})
```
Import your style file to `src/style.scss`
```css
/* style.scss */
import 'your-custom-style';
```
Or add your style file to the `styles` arrays which in `angular.json` file 
```json
// angular.json
{
   // other configurations 
  "projects": {
    "YourProject": {
      // other configurations
      "architect": {
        "build": {
            "styles": [
              // other styles  
              "your-custom-style-file"
            ],
          },
        },
        "test": {
          "options": {
            "styles": [
               // other styles  
              "your-custom-style-file"
            ],
          }
        },
      }
    }
  }
}
```