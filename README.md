# Target Observer

## Table of Contents

-   [Introduction](#target-observer)
-   [Component API](#component-api)
    -   [InViewProvider](#inviewprovider)
    -   [useInView](#useinview)
    -   [ObserveZone](#observezone)
    -   [Target](#target)
-   [Examples](#examples)
    -   [React + Css](#react-with-css)
    -   [React + Tailwind Css](#react-with-tailwind-css)

**Target Observer** is designed to help developers efficiently monitor and manage the visibility of elements within the viewport.

It provides a set of tools including the `useInView` hook, `InViewProvider`, `Target`, and `ObserveZone` components. These tools enable seamless tracking of when specific elements, identified as "targets" enter or exit the viewport.

With `Target Observer`, you can easily trigger animations, lazy load content, or handle visibility-based logic in your application. The library is optimized for performance and ease of use, making it a robust solution for building responsive and dynamic user interfaces.

Checkout the demo [here](https://target-observer-demo.vercel.app/).

## Component API

### InViewProvider

The `InViewProvider` component is a wrapper for tracking the visibility of multiple `Target` components within the viewport. The component monitors when each target enters or exits the viewport, enabling you to manage and respond to the in-view state of these elements within your application.

| Property                   | Default | Description                                                                                                                                                                  |
| -------------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `targetIds`                | -       | An array of strings that specifies the IDs of the Target components to be tracked by the InViewProvider.                                                                     |
| `firstTargetActiveOnMount` | true    | By default, the first target is activated when the component mounts. If set to false, it will only update once the user starts scrolling and the target enters the viewport. |

### useInView

The useInView hook tracks when a target element enters the viewport, making it ideal for dynamically updating the styling of items or chapters in a table of contents on a documentation site.

### ObserveZone

The `ObserveZone` is an invisible component that defines a specific area within which visibility of child elements is tracked.

| Property    | Default | Description                                                                        |
| ----------- | ------- | ---------------------------------------------------------------------------------- |
| `height`    | 50vh    | An optional property for adjusting the observing area's height.                    |
| `className` | -       | An optional property for defining class names. Use only for testing the component. |

### Target

The `Target` component represents an element whose visibility is tracked within the `ObserveZone`. Each `Target` should have a unique ID that matches an entry in the `targetIds` array provided to the `InViewProvider`.

The component’s visibility state can be used to trigger actions, such as animations or lazy loading, based on whether the target is in view or out of view.

| Property         | Default | Description                                                                                                                                                                                                                      |
| ---------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`             | -       | The id of the component to track and trigger state updates.                                                                                                                                                                      |
| `as`             | div     | The element or component the Target should render as.                                                                                                                                                                            |
| `entryThreshold` | 0.5     | The percentage of observing zone height that should be used to trigger the observer. The target element when inside this area will update the inView property to true. Accepts value between 0 and 1. Min is 0.1 and Max is 0.9. |

## Examples:

### React with CSS

```tsx
import clsx from 'clsx'
import { useInView, InViewProvider, ObserveZone, Target } from 'target-observer'

const targetIds = [
    'section#1',
    'section#2',
    'section#3',
    'section#4',
    'section#5'
]
export default function Example() {
    return (
        <InViewProvider targetIds={targetIds}>
            <div className='container'>
                <Navigation />

                {/* must use position:relative in parent for ObserveZone to work */}
                <div className='target-wrapper'>
                    {/* add this invisible component to track the target */}
                    <ObserveZone
                        // optional height property, default is 50vh
                        height='70vh'
                        // optional className property, use only for testing
                        className='observer'
                    />

                    {targetIds.map((targetId) => (
                        <Target
                            key={targetId}
                            // must specify the id property for target to work
                            id={targetId}
                            // the html element you want to render
                            as='section'
                            // add styling to your target
                            className='target'
                            // the height in percent of observing zone to trigger inView state
                            // default is 0.5 (50 percent)
                            entryThreshold={0.3}
                        >
                            Target: {targetId}
                        </Target>
                    ))}
                </div>
            </div>
        </InViewProvider>
    )
}

function Navigation() {
    const inView = useInView() // returns a record with boolean values

    return (
        <nav>
            <ul>
                {targetIds.map((targetId) => {
                    return (
                        <li
                            key={targetId}
                            className={clsx({
                                'list-active': inView[targetId]
                            })}
                        >
                            <a href={'#' + targetId}>{targetId}</a>
                        </li>
                    )
                })}
            </ul>
        </nav>
    )
}
```

```css
nav {
    position: sticky;
    top: 2.5rem;
    height: 100vh;
}

ul {
    list-style-type: none;
}

li {
    width: 8rem;
    text-align: center;
    color: black;
}

a {
    text-decoration: none;
    font-size: 18px;
    color: inherit;
}

li:not(:first-child):not(:last-child),
section:not(:first-child):not(:last-child) {
    margin-top: 16px;
    margin-bottom: 16px;
}

.list-active {
    font-weight: 700;
    padding: 0.25rem 0 0.25rem 0;
    background-color: #ef4444;
    color: white;
}

.container {
    max-width: 80rem;
    width: 100%;
    margin-left: auto;
    margin-right: auto;
    padding: 1.25rem;
    display: flex;
    gap: 2.5rem;
}

.target-wrapper {
    position: relative;
    width: 100%;
}

.target {
    height: 90vh;
    border: 4px solid #e5e7eb;
    padding: 1.25rem;
}

.observer {
    outline: 4px solid rgba(239, 68, 68, 0.2);
    outline-offset: 8px;
}
```

### React with Tailwind CSS

```tsx
import clsx from 'clsx'
import { useInView, InViewProvider, ObserveZone, Target } from 'target-observer'

const targetIds = [
    'section#1',
    'section#2',
    'section#3',
    'section#4',
    'section#5'
]
export default function Example() {
    return (
        <InViewProvider targetIds={targetIds}>
            <div className='h-screen w-full'>
                <div className='max-w-7xl mx-auto w-full p-5 flex gap-10'>
                    <Navigation />
                    {/* must use position:relative in parent for ObserveZone to work */}
                    <div className='relative w-full space-y-5'>
                        {/* add this invisible component to track the target */}
                        <ObserveZone
                            // optional height property, default is 50vh
                            height='70vh'
                            // optional className property, use only for testing
                            className='ring-4 ring-offset-8 ring-red-500/20'
                        />

                        {targetIds.map((targetId) => (
                            <Target
                                key={targetId}
                                // must specify the id property for target to work
                                id={targetId}
                                // the html element you want to render
                                as='section'
                                // add styling to your target
                                className='h-[90vh] border-4 p-5 w-full'
                                // the height in percent of observing zone to trigger inView state
                                // default is 0.5 (50 percent)
                                entryThreshold={0.3}
                            >
                                Target: {targetId}
                            </Target>
                        ))}
                    </div>
                </div>
            </div>
        </InViewProvider>
    )
}

function Navigation() {
    const inView = useInView() // returns a record with boolean values
    return (
        <div className='sticky top-10 h-screen'>
            <ul className='space-y-4'>
                {targetIds.map((targetId) => (
                    <li
                        key={targetId}
                        className={clsx('text-center w-32', {
                            'font-bold py-1 bg-red-500 text-white':
                                inView[targetId]
                        })}
                    >
                        <a href={'#' + targetId}>{targetId}</a>
                    </li>
                ))}
            </ul>
        </div>
    )
}
```

## Issues

Please file an issue for bugs, missing documentation, or unexpected behavior.
Don't hesitate to make a PR for any issue.

[File an issue](https://github.com/rawjson/target-observer/issues)
