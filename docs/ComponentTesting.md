# Component Testing

A system can be broken down into **components** (also known as **modules**). They can be thought of like interchangeable parts of a machine: each component/module should be in an **independent** and **controllable** state. For example, a controller is a component that decides which other components to call for a request.

---

## Overview of component testing

- Verify the input and output behavior of the system
- Test individual components/modules in **isolation**
- Verify the **functionality, usability and state** of each component *independently*
- Test a **single** component
- It is common to use mocks or stubs to **simulate** interactions with other components
