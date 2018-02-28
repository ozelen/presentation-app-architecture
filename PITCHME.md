@title[Clean Code]

# Application Architecture

#### Introduction

[Presentation Link](https://github.com/ozelen/presentation-app-architecture)

<br>
<br>
<span class="byline">by Oleksiy Zelenyuk</span>

#VSLIDE

# About Me

#VSLIDE

## Oleksiy Zelenyuk
![Me](assets/images/me.png)

- ozelen@softserveinc.com
- Skype: oleksa.zelenyuk
- Tel. 1531 (int)
- Github: [ozelen](https://github.com/ozelen/)

#VSLIDE

## In love with JS since 1999

![Logo](assets/images/this.png)

#VSLIDE

## Currently

- COE Solutions                           <!-- .element: class="fragment" -->
- Architect at MK for last 6 months       <!-- .element: class="fragment" -->
- Discover and kickstart new projects     <!-- .element: class="fragment" -->
- Onboarding new comers                   <!-- .element: class="fragment" -->
- Leading Web UI comptence group          <!-- .element: class="fragment" -->

#HSLIDE
## AGENDA

- What is Architecture          <!-- .element: class="fragment" -->
- Main Principles               <!-- .element: class="fragment" -->
- Application Architecture      <!-- .element: class="fragment" -->
- Patterns & Anti-Patterns      <!-- .element: class="fragment" -->

#HSLIDE

## What is Architecture

![Architecture](assets/images/architecture.png)

#VSLIDE

## What is Architecture

- A structure is a set of elements and the relations among them.                    <!-- .element: class="fragment" -->
- Can be presented by a View                                                        <!-- .element: class="fragment" -->
- A product of a single architect or a small team of architects with a tech leader  <!-- .element: class="fragment" -->
- Delivers the system's important quality attributes.                               <!-- .element: class="fragment" -->

#VSLIDE

## Architectural goals

- Define system modules                                         <!-- .element: class="fragment" -->
- Make system more predictable and easier to change             <!-- .element: class="fragment" -->
- Make decisions clean and documented                           <!-- .element: class="fragment" -->
- Tooling-Agnostic                                              <!-- .element: class="fragment" -->

#VSLIDE

## Difference from other Architectures

- Code is much easier to change than materials, e.g. walls      <!-- .element: class="fragment" -->
- Requirements for Software are less deterministic              <!-- .element: class="fragment" -->
- Implementation Process is less deterministic as well          <!-- .element: class="fragment" -->
- Very domain specific                                          <!-- .element: class="fragment" -->

#VSLIDE

## Summary

> The software architecture of a system is the set of structures needed to reason
> about the system, which comprise software elements, relations among them, and
> properties of both.

***
*Software Architecture in Practice, Ch. 1 -- What Is Software Architecture?*

#HSLIDE

# Functional vs. Non-functional requirements

#HSLIDE

# Attribute Driven Design

## Quality attributes (aka Non-Func Requirements)

Stands for tracking system's abilities

- Availability.
- Conceptual Integrity.
- Interoperability.
- Maintainability.
- Security.
- Performance.
- Reliability.
- Reusability.
- ...

#HSLIDE

# Domain Driven Design

Teaches how to break down system elements and name things.

- Ubiquitous language
- Boundary context
- Domain-specific module structure
- Anti-corruption layer
- ...

#HSLIDE

## Architectural scope

1. Architecture for change                           <!-- .element: class="fragment" -->
1. Domain Specific                                   <!-- .element: class="fragment" -->
1. Project structure decomposition                   <!-- .element: class="fragment" -->
1. Dependency Management                             <!-- .element: class="fragment" -->
1. Code Standards                                    <!-- .element: class="fragment" -->
1. Pattern-oriented Architecture                     <!-- .element: class="fragment" -->
1. Continuous Refactoring                            <!-- .element: class="fragment" -->

#HSLIDE

## Architecture for change

### Attributes

- Maintainability    <!-- .element: class="fragment" -->
- Configurability    <!-- .element: class="fragment" -->
- Modifiability      <!-- .element: class="fragment" -->

#VSLIDE

## Reduce Dependencies

- Decouple modules and components
- Messaging patterns (Pub/Sub, Message bus, Redux, etx)
- Manage and reduce external dependencies
- Inversion of control Principle
- Dependency Inversion Principle (SOLID)

#VSLIDE

## Dependency Inversion

#VSLIDE

## Antipattern: Dependency Hell

```ts
import axios from 'axios';

const getSomeData = () =>
    axios.get(SOME_API_URL).
        then(actions.updateDataSuccess).
        catch(actions.updateDataFail);
```

#VSLIDE

## Wrap Dependencies

```ts
import http from '../services/http';

const getSomeData = () => http.get(SOME_API_URL).
    then(actions.updateDataSuccess).
    then(actions.updateDataFail);
```

#VSLIDE

## Inversion of control (IoC)

```js
// Big config
<AppFrame config={{
    authConfig,
    themeConfig,
    breadCrumbsConfig,
    ...
}} />

// Lego-style IoC
<App theme={theme}>
    <Authorization />
    <AppHeader>
        <BreadCrumbs>
            <Breadcrumb href={'/...'} name={'Home'} />
            <Breadcrumb href={'/...'} name={'Inner Page'} />
            ...
        </BreadCrumbs>
    </AppHeader>
    <Menu elements={[...]} />
</App>
```

@[1-7](Component knows and everything inside)
@[9-20](Component is a wrapper and exposes control to parent)

#VSLIDE

## IoC Examples

- Observables
- Promises
- Dependency injection
- Strategy pattern

#VSLIDE

## DI + IoC

#VSLIDE

## Invert and segregate

```ts
interface IApi<Model> {
    baseUrl: string;
    getList(): Observable<Model[]>;
    get(id: number): Observable<Model>;
    create(data: Model): Observable<Model|Error>;
    update(data: Model): Observable<Model|Error>;
    destroy(id: number): Observable<Model|Error>;
}

class RestApi<Model> implements IApi {
    readonly baseUrl: string;
    readonly resource: string;

    constructor({baseUrl, resource}) {
        this.baseUrl = baseUrl;
        this.resource = resource;
    }

    getItemList(): Model[] {
        return axios.get(`${this.baseUrl}/${resource}/`);
    }

    getItem(id: number): Model {
        return axios.get(`${this.baseUrl}/${resource}/${id}`);
    }

    ...
}

class FeatureApi extends RestApi<SomeFeatureModel>({
    baseUrl: SOME_API_URL,
    resource: 'some-feature'
});

const api = new RestApi(FeatureApi);
const getDataFromData = () =>
    api.getItems();

export const pollingApi = (action$: ActionsObservable<Action>) =>
  action$.ofType(actions.START_POLLING).
    flatMap(action =>
      Observable.interval(POLLING_INTERVAL).
        takeWhile(someCondition).
        flatMap(getDataFromData).
        map(normalize).
        map(actions.updateFeatureStore));
```

@[1-8](Create an abstract interface)
@[10-28](Implement concrete strategy)
@[30-33](Extend with parameters)
@[35-37](Use in a system component)
@[38-49](Add IoC by Observables and FRP)

#HSLIDE

## Leverage Standard Interfaces

- Promises              <!-- .element: class="fragment" -->
- Observables           <!-- .element: class="fragment" -->
- Node Streams & Pipes  <!-- .element: class="fragment" -->
- REST                  <!-- .element: class="fragment" -->
- HATEOAS               <!-- .element: class="fragment" -->
- GraphQL               <!-- .element: class="fragment" -->
- OData                 <!-- .element: class="fragment" -->

#VSLIDE

## Tooling-Agnostic architecture

- Design data flows and system elements.                               <!-- .element: class="fragment" -->
- Platforms and libraries you use are important, but could be changed. <!-- .element: class="fragment" -->
- Rather rely on interfaces than on tools and platforms.               <!-- .element: class="fragment" -->

#VSLIDE

# Domain-specific architecture

## Antiapttern: Universe Architecture

![universe](assets/images/universe.png)

Too abstract and soft approach, ends up as too vague and complex in implementation.


#HSLIDE

## Clean coding principles (e.g. SOLID)

- Use them             <!-- .element: class="fragment" -->
- Don't overthink      <!-- .element: class="fragment" -->

#HSLIDE

## Separation of Concerns and Components

```js
├── src
│   ├── app
│   │   ├── App.container.tsx
│   │   ├── app.css
│   │   └── index.tsx
│   ├── store
│   │   ├── actions.ts
│   │   ├── index.ts
│   │   ├── middlewares.ts
│   │   ├── reducers.ts
│   │   └── state.ts
│   ├── features
│   │   └── menu
│   │   │   ├── MenuItem.tsx
│   │   │   ├── Menu.container.tsx
│   │   │   ├── Menu.spec.ts
│   │   │   ├── aggregation.test.ts
│   │   │   ├── aggregation.ts
│   │   │   ├── api.ts
│   │   │   ├── index.ts
│   │   │   ├── menu.actions.ts
│   │   │   ├── menu.constants.ts
│   │   │   ├── menu.css
│   │   │   ├── menu.d.ts
│   │   │   ├── menu.epics.ts
│   │   │   └── menu.reducer.ts
│   │   ├── index.ts
│   ├── utils
│   │   ├── index.ts
│   │   ├── sort.test.ts
│   │   └── sort.ts
│   ├── index.html
│   └── index.tsx
├── Makefile
├── types
├── package.json
├── webpack.config.js
├── tsconfig.json
├── README.md
```

@[34-39](Root configuration files)
@[1-5](Application container and common layouts)
@[6-11](Common framework settings and services)
@[28-31](Utils and other common stuff)
@[12-26](Features package)
@[14-15](UI Components)
@[16-17](Unit and integration tests)
@[18-25](Other related files)

#HSLIDE

#VSLIDE

### Avoid

# Analysis Paralysis

![analysis](assets/images/analysis-paralysis.png)

## Continuous Refactoring and Rethinking

#VSLIDE

##
![analysis](assets/images/vasa.jpg)

## Continuous Refactoring and Rethinking

#VSLIDE

## Continuous Cleanup

![analysis](assets/images/cleanup.jpg)

#HSLIDE

## References

![analysis](assets/images/cleanup.jpg)

- [Software Architecture in Practice (3rd Edition) (SEI Series in Software Engineering)](https://www.amazon.com/Software-Architecture-Practice-3rd-Engineering/dp/0321815734)
- [MSDN - Chapter 16: Quality Attributes](https://msdn.microsoft.com/en-us/library/ee658094.aspx)
- [Vasa Ship](https://en.wikipedia.org/wiki/Vasa_(ship))
