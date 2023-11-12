# Customize Material UI

Customise Material UI for specific needs.

Designed for typescript users to handle type errors and enable auto-code-completion.

**Current Material UI version: 5.14.17**

**Date: 12th November, 2023**

### Table of Contents:
1. [Create custom theme using mui’s createTheme function](#section-1-create-custom-theme-using-muis-createtheme-function)
1. [Adding a custom theme level property](#section-2-adding-a-custom-theme-level-property)
1. [Adding a custom theme-palette property](#section-3-adding-a-custom-theme-palette-property)
1. [Adding a custom property inside a palette-color](#section-4-adding-a-custom-property-inside-a-palette-color)
1. [Conclusion](#conclusion)
1. [About the Developer](#about-the-developer)

## Section 1: Create custom theme using mui's `createTheme` function

In order to create our own customised theme, we need to use `createTheme` function.

```js
import { Box, colors, createTheme, ThemeProvider } from "@mui/material";

// some code here

export default function App() {
    const theme = createTheme({
        palette: {
            secondary: {
                main: colors.orange[500]
            }
        }
    });

    return (
        <ThemeProvider theme={theme}>
            /* the box below will have orange[500] color as defined in the createTheme function */
            <Box sx={{ bgColor: "secondary.main" }} />
        </ThemeProvider>
    );
}
```

## Section 2: Adding a custom theme level property

#### Section 2.1

In order to create a custom theme level property, we can simply add the property inside the `createTheme` function parameter object.

```js
import { Box, createTheme, ThemeProvider } from "@mui/material";

// some code here

export default function App() {
    const theme = createTheme({
        status: {
            danger: "#e53e3e"
        }
    });

    return (
        <ThemeProvider theme={theme}>
            /* the box below will have orange[500] color as defined in the createTheme function */
            <Box sx={{ bgColor: "secondary.main" }} />
        </ThemeProvider>
    );
}
```

If you are using typescript, this will through an error. For countering this error, we need to create a file known as the **_theme.d.ts_** in the **_src/_** directory in our react/next app.

After creating the file, make the necessary adjustments as shown below. The code is pretty self explanatory and I'm pretty sure you will get it on your own.

```js
import { CSSProperties } from "react";
import { ThemeOptions } from "@mui/material/styles";

declare module "@mui/material/styles" {
    interface ThemeOptions {
        status: {
            danger: CSSProperties["color"]
        }
    }
}
```

#### Section 2.2

The above solution fixes the error but there is still a problem. The text editor will not be able to use auto code completion functionality for the customized property.

Moreover, it also throws an error stating that: `Property "status" does not exist on type "Theme".`

```js
import { Box, styled } from "@mui/material";

export default function SomeRandomComponent() {
    const StyledBox = styled(Box)(({ theme }) => ({
        backgroundColor: theme.styled.danger // error will be thrown here
    }))

    return (
        <StyledBox />
    );
}
```

To counter this, add the following lines of code to the existing theme.d.ts file

```js
import { CSSProperties } from "react";
import { ThemeOptions } from "@mui/material/styles";

declare module "@mui/material/styles" {
    interface Theme {
        status: {
            danger: string
        }
    }

    interface ThemeOptions {
        status: {
            danger: CSSProperties["color"]
        }
    }
}
```

This will fix the issue

#### Section 2.3: Conclusion

From Step 2, here is the conclusion.

In **section 2.1**, we tried to add a custom property at the theme level which threw an error as material ui does not contain the types for that custom property by default. Therefore, we had to extend the **_theme.d.ts_** to add our desired property.

This does not fix the issue completely, because when we try to use that custom property inside a component, it throws an error. In order to counter that error, we added more configuration inside the **_theme.d.ts_** file inside **section 2.2**.

## Section 3: Adding a custom theme-palette property

Let's say we want to add custom colors to our website and for that we need to add custom theme-palette properties to our design system.

```js
import { Box, colors, createTheme, ThemeProvider } from "@mui/material";

// some code here

export default function App() {
    const theme = createTheme({
        palette: {
            secondary: {
                main: colors.orange[500]
            },
            myCustomProperty: {
                main: colors.grey[500]
            }
        }
    });

    return (
        <ThemeProvider theme={theme}>
            /* the box below will have orange[500] color as defined in the createTheme function */
            /* Components go here */
        </ThemeProvider>
    );
}

```

Again, you will see red squiggly lines and you already know the reason (that is, if you followed along 😄). Now to counter it, add the following code to your **_theme.d.ts_** file.

```js
import { PaletteColor, PaletteColorOptions } from "@mui/material/styles";

declare module "@mui/material/styles" {
    // counters the errors when trying to use the custom palette-property inside our application
    interface Palette {
        myCustomProperty?: PaletteColor
    }

    // counters the errors inside the createTheme function
    interface PaletteOptions {
        myCustomProperty?: PaletteColorOptions
    }
}

```

## Section 4: Adding a custom property inside a palette-color

As we already know that each of the main elements of the palette contain four elements viz. **_main, light, dark, contrastText_**. Let's say we want to customize this behavior and add an even darker property to one of these elements.

```js
import { Box, colors, createTheme, ThemeProvider } from "@mui/material";

// some code here

export default function App() {
    const theme = createTheme({
        palette: {
            secondary: {
                main: colors.orange[500]
            },
            myCustomProperty: {
                main: colors.grey[500],
                darker: colors.grey[800] // our custom property
            }
        }
    });

    return (
        <ThemeProvider theme={theme}>
            /* the box below will have orange[500] color as defined in the createTheme function */
            /* Components go here */
        </ThemeProvider>
    );
}
```

Now to fix the typescript errors, add the following elements in your existing **_theme.d.ts_** file.

```js
import { PaletteColor, PaletteColorOptions } from "@mui/material/styles";

declare module "@mui/material/styles" {
    interface Palette {
        myCustomProperty?: PaletteColor
    }

    interface PaletteOptions {
        myCustomProperty?: PaletteColorOptions
    }

    // add the following configuration
    // counters errors while creating custom property
    interface SimplePaletteColorOptions {
        darker?: string
    }

    // helps with autocompletion
    interface PaletteColor {
        darker?: string
    }
}
```

That's it for this tutorial, the complete **_theme.d.ts_** file is below.

```js
import { CSSProperties } from "react";
import { PaletteColor, PaletteColorOptions, ThemeOptions } from "@mui/material/styles";

declare module "@mui/material/styles" {
    interface ThemeOptions {
        status: {
            danger: CSSProperties["color"]
        }
    }

    interface ThemeOptions {
        status: {
            danger: CSSProperties["color"]
        }
    }

    interface Palette {
        myCustomProperty?: PaletteColor
    }

    interface PaletteOptions {
        myCustomProperty?: PaletteColorOptions
    }

    interface SimplePaletteColorOptions {
        darker?: string
    }

    interface PaletteColor {
        darker?: string
    }
}
```

## Conclusion

1. For adding custom properties at theme level, add the following (here, we are taking status.danger as an example)

```js
import { CSSProperties } from "react";
import { ThemeOptions } from "@mui/material/styles";

declare module "@mui/material/styles" {
    interface ThemeOptions {
        status: {
            danger: CSSProperties["color"]
        }
    }

    interface ThemeOptions {
        status: {
            danger: CSSProperties["color"]
        }
    }
}
```

2. For adding custom properties at theme-palette level, add the following (here, we are taking myCustomProperty as an example)

```js
import { PaletteColor, PaletteColorOptions } from "@mui/material/styles";

declare module "@mui/material/styles" {
    interface Palette {
        myCustomProperty?: PaletteColor
    }

    interface PaletteOptions {
        myCustomProperty?: PaletteColorOptions
    }
}
```

3. For adding additional properies inside each theme-palette property, add the following (here, we are taking myCustomProperty.darker as an example)

Example: We already know that theme.palette.primary contains the following properties: main, light, dark, contrastText. Now, say we want to add another property called darker, this needs typescript configurations to handle type related errors, so here's how we counter it

```js
declare module "@mui/material/styles" {
    interface SimplePaletteColorOptions {
        darker?: string
    }

    interface PaletteColor {
        darker?: string
    }
}
```

If you find it confusing than here's the reference to mui's github code which help you understand things better: [Link to github's theme customization link file](https://github.com/mui/material-ui/blob/master/packages/mui-material/src/styles/createPalette.d.ts)

Feel free to contact me for any correction or any complaints regarding this tutorial: [Contact Link](https://angkushsahu.vercel.app/contact)

## About the Developer

Contact Me from [here](https://angkushsahu.vercel.app/contact)

[![portfolio](https://img.shields.io/badge/my_portfolio-teal?style=for-the-badge&logo=ko-fi&logoColor=white)](https://angkushsahu.vercel.app/)
[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/angkush-sahu-0409311bb)
[![mail](https://img.shields.io/badge/Mail-red?style=for-the-badge&logo=gmail&logoColor=white)](https://angkushsahu.vercel.app/contact)
[![github](https://img.shields.io/badge/Github-gray?style=for-the-badge&logo=github&logoColor=white)](https://github.com/angkushsahu)
