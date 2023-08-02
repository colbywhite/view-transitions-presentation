---
theme: seriph
transition: slide-left
layout: cover
defaults:
  layout: quote
favicon: 'https://remix.run/favicon-192.png'
background: ''
---

# Page transitions via the Platform

Can we get SPA-like page transitions in a MPA?

By Colby M. White ([colbywhite.dev](https://colbywhite.dev))

---
layout: two-cols
class: flex align-center justify-center flex-col
---

# Navigation in MPAs

- Full-page reloads are typically required to navigate to a new page
- In exchange, navigation is simply a new `*.html` file

::right::

# Navigation in SPAs

- Navigation is typically done via JavaScript, not the browser
- A better user experience is usually the result, but developer complexity increases

---
layout: two-cols
---

# Enter the View Transition API

This is a browser-native way to have more control of navigation - _even in a MPA_.

It is mostly CSS powered, but adding JavaScript logic is possible.

::right::

<iframe width="560" height="315" src="https://www.youtube.com/embed/JCJUPJ_zDQ4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Google has docs on the subject, including the above video (from Jake Archibald) and the _Smooth and simple transitions with the View Transitions API_ tutorial at https://developer.chrome.com/docs/web-platform/view-transitions/

---
layout: center
---

# Note: It's not in all browsers yet

<div class="p-2 flex justify-center">
    <picture>
        <source type="image/webp" srcset="https://caniuse.bitsofco.de/static/v1/view-transitions-1686804434904.webp">
        <source type="image/png" srcset="https://caniuse.bitsofco.de/static/v1/view-transitions-1686804434904.png">
        <img src="https://caniuse.bitsofco.de/static/v1/view-transitions-1686804434904.jpg" alt="Data on support for the view-transitions feature across the major browsers from caniuse.com">
    </picture>
</div>

---
layout: no-transition-demo
---

# Standard MPA navigation Example

### Transition between pages is a full-page reload

1. Old page disappears
2. Browser shows white screen while new page loads
3. New page appears

<br />

> Most end up using a lot of JavaScript tyring to come up with a better experience ... loading icons, partial changes, animations, etc, etc

---
layout: two-cols
class: flex align-center justify-center flex-col
---

# How can the View Transition API help?

- DOM updates can be wrapped in a `startViewTransistion`
- Default transition is a cross-fade

::right::

```js
// Fallback for browsers that don't support this API:
if (!document.startViewTransition) {
    updateTheDOMSomehow(data);
    return;
}

// With a transition:
document.startViewTransition(
    () => updateTheDOMSomehow(data)
);
```

---
layout: two-cols
class: flex align-center justify-center flex-col
---

# Apply transitions on navigation (in Vanilla JS)

- Use the Navigation API to listen for the event
- Retrieve whichever data is needed for the new page
- Pipe results to `startViewTransition`

::right::

```js {4,8,10,16-18}
// Intercept navigations
// https://developer.chrome.com/docs/web-platform/navigation-api/
// This is a naive usage of the navigation API, to keep things simple.
navigation.addEventListener('navigate', (event) => {
    const toUrl = new URL(event.destination.url);
    if (location.origin !== toUrl.origin) return;

    event.intercept({
        async handler() {
            const data = await getContent(toUrl)
            if (!document.startViewTransition) {
                updateTheDOMSomehow(data);
                return;
            }

            document.startViewTransition(
                () => updateTheDOMSomehow(data)
            );
        },
    });
});
```

---
layout: default-transition-demo
---

# Result

Simple cross-fade between pages.

---
layout: iframe-right
url: https://simple-set-demos.glitch.me/1-cross-fade/
---

# Demo from Google

Source: https://developer.chrome.com/docs/web-platform/view-transitions/
---
layout: two-cols
class: flex align-center justify-center flex-col
---

# So what's happening?

- A snapshot of the old content is created
- Browser create a psuedo element tree
- A CSS animation is applied to the old content (from `opacity: 1` to `opacity: 0`) and the new content (
  from `opacity: 0` to `opacity: 1`)

::right::

```
::view-transition
└─ ::view-transition-group(root)
└─ ::view-transition-image-pair(root)
├─ ::view-transition-old(root)
└─ ::view-transition-new(root)
```

---
layout: iframe-right
url: https://simple-set-demos.glitch.me/2-slow-cross-fade/
---

# Use CSS to style the transition

```css
::view-transition-old(root),
::view-transition-new(root) {
    animation-duration: 5s;
}
```

---
layout: iframe-right
url: https://simple-set-demos.glitch.me/3-shared-axis/
---

# Get fancy

```css
@keyframes fade-in {
    from {
        opacity: 0;
    }
}

@keyframes fade-out {
    to {
        opacity: 0;
    }
}

@keyframes slide-from-right {
    from {
        transform: translateX(30px);
    }
}

@keyframes slide-to-left {
    to {
        transform: translateX(-30px);
    }
}

::view-transition-old(root) {
    animation: 90ms cubic-bezier(0.4, 0, 1, 1) both fade-out,
    300ms cubic-bezier(0.4, 0, 0.2, 1) both slide-to-left;
}

::view-transition-new(root) {
    animation: 210ms cubic-bezier(0, 0, 0.2, 1) 90ms both fade-in,
    300ms cubic-bezier(0.4, 0, 0.2, 1) both slide-from-right;
}
```

---
layout: iframe-right
url: https://simple-set-demos.glitch.me/4-fixed-header/
---

# `view-transition-name` isolates an element

```css {2}
.main-header {
    view-transition-name: main-header;
}
```

```css {2,6}
::view-transition
├─ ::view-transition-group(root)
│ └─ ::view-transition-image-pair(root)
│ ├─ ::view-transition-old(root)
│ └─ ::view-transition-new(root)
└─ ::view-transition-group(main-header)
└─ ::view-transition-image-pair(main-header)
├─ ::view-transition-old(main-header)
└─ ::view-transition-new(main-header)
```

---
layout: iframe-right
url: https://simple-set-demos.glitch.me/5-heading-text/
---

# Get fancy

```css
.main-header-text {
    view-transition-name: main-header-text;
    width: fit-content;
}
```

```html
<!-- page 2 -->
<header class="main-header">
    <a href="./" class="back-and-title">
        <svg class="back-icon">...</svg>
        <span class="main-header-text">
          Demo site
        </span>
    </a>
</header>
```

---
layout: fact
---

## Bonus personal takeaway:
## CSS animations are powerful af 🤔

... but this isn't a CSS talk

---

# Can we do this in Remix?

Remix embraces the platform so this should be easy ... _right_?

---
layout: iframe-right
url: https://no-animation--gilded-lamington-b32b07.netlify.app
---

# Basic Remix app

Using `NavLink` to build a simple navigation bar.

```tsx
<ul className="menu menu-horizontal w-full justify-center gap-2">
    <li>
        <NavLink
            to="/"
            className={({isActive}) =>
                isActive ? ACTIVE_NAV_LINK_CLASSES : NAV_LINK_CLASSES
            }
        >
            Home
        </NavLink>
        {pathname === to && <ActiveNavMarker/>}
    </li>
</ul>
```

---
layout: iframe-right
url: https://default-animation--gilded-lamington-b32b07.netlify.app/
---

# Wrap `NavLink` with `startViewTransition`

```tsx {all|7,13-17}
export default function AnimatedNavLink({
                                            children,
                                            to,
                                            ...rest
                                        }: ComponentProps<typeof NavLink>) {
    const {pathname} = useLocation();
    const navigate = useNavigate();
    return (
        <>
            <NavLink
                {...rest}
                to={to}
                onClick={(event) => {
                    event.preventDefault();
                    startViewTransition(
                        () => navigate(to)
                    );
                }}
            >
                {typeof children === "function"
                    ? ({...props}) => children(props)
                    : children}
            </NavLink>
            {pathname === to && <ActiveNavMarker/>}
        </>
    );
}
```

---
layout: two-cols
class: flex align-center justify-center flex-col
---

# Vanilla JS

```js
navigation.addEventListener('navigate', (event) => {
    event.intercept({
        async handler() {
            const data = await getContent(toUrl)
            document.startViewTransition(
                () => updateTheDOMSomehow(data)
            );
        },
    });
});
```

::right::

# Remix

```tsx
const navigate = useNavigate();
return (
    <NavLink to={to}
             onClick={(event) => {
                 event.preventDefault();
                 startViewTransition(
                     () => navigate(to)
                 );
             }}
    />
)
```

---
layout: iframe-right
url: https://gilded-lamington-b32b07.netlify.app/
---

# Get fancy

```css
#active-marker {
    view-transition-name: active-nav
}
```

```css {6-9}
::view-transition
├─ ::view-transition-group(root)
│ └─ ::view-transition-image-pair(root)
│ ├─ ::view-transition-old(root)
│ └─ ::view-transition-new(root)
└─ ::view-transition-group(active-nav)
└─ ::view-transition-image-pair(active-nav)
├─ ::view-transition-old(active-nav)
└─ ::view-transition-new(active-nav)
```

---
layout: iframe-right
url: https://remix.run/docs/en/1.17.1/hooks/use-transition
---

# Wait, what's `useTransition`?

The Remix `useTransition` hook is changing its name to `useNavigation`. The use case is more around Pending and
Optimistic UI, not transitions. (Hence the name change perhaps?)

---
layout: cover
background: ''
---

# Conclusion 
## You don't _need_ a <u>SPA framework</u> to get <u>SPA-like transitions</u>
