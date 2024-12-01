# Lightening web components

Two ways to build

- **"Aura Components"** programming model - structured modular re-usable components. (considered legacy)

  - MVC pattern (model, view, controller)
  - Event driven. Can broadcast events to be picked up by other components
  - Server and client side
  - Component code looks like old React class components

- **"Lightning Web Components"**

  - Uses ["web components"](https://github.com/WICG/webcomponents)
  - Embracing standards (Shadow DOM etc)
  - compiled by SF
  - considered faster

Two can co-exist.
Aura can contain lightning components, but lightening can't contain aura

## Lightning Data Service (LDS)

Data cached across components
Can use a "wire service" to get methods

Can call [Apex](./apex.md) methods. Limits apply
