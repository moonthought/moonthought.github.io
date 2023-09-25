+++
title = "All your mainstream UI frameworks are lying to you"
date = 2023-09-25T13:25:39+03:00
draft = false
+++

What a lovely way to start an article. But don't get me wrong — this is not a clickbait.
And I'm going to name-drop some technologies here and there. Not to tarnish their reputation, but to better reveal a thought that has haunted me for a long time.

In addition, this article is not a marketing one and is not intended to promote some other magic pill.

## It all ended with Svelte

A few days ago, a teaser of Svelte 5 was unveiled with their [introduction of runes](https://svelte.dev/blog/runes).

And almost everyone got excited about it. And I'm excited too. Excited, because I've already seen this approach to reactivity 5-6 years ago.
All those `$props`, `$derived`, `$effects`, `$state`, signals – I've seen it all before. And I like it. And I think they're on the right track. Tho Svelte still clinging to mainstream solutions that have shown themselves incapable of conveniently solving complex web problems, but hopefully they'll get over it. But that's not what we're going to talk about.

What really pissed me off was is the same kludge to solve UI problems.

Why does the solution for reactivity in components still require compilation? Why is hijacked HTML syntax with fake directives still being used? Why is the UI representation still described in an imperative way? And why is technology still trying to mimic HTML in the first place?

Let's start with the latter.

## Who says HTML is the right abstraction?

A provocative question? Perhaps. But HTML itself is nothing more than a projection of the DOM tree. It's just one way of representing that tree, and no one has said that this way is optimal enough. And rest assured, it's not.

Browsers do not deal directly with HTML, but with DOM nodes. And to fully describe each DOM node, there should be seven categories of properties:
* attributes
* handlers
* styles
* data-* attributes
* visibility
* text content
* children

And unfortunately, many developers don't realise or don't want to realise that we don't need to hide from this complexity*. It's our platform, and it's our responsibility to figure it out.
Unfortunately, almost all UI solutions fool themselves and us time and time again by using the most primitive technique - the suggestion of simplicity.

They all try to reduce the diversity of DOM-node properties to a flat list of attributes. It doesn't work. And it's perfectly visible. Reducing at least seven categories of DOM-node properties to a flat list of attributes doesn't make life any easier, there are still seven categories, they just turn into flat mincemeat.

— *complexity is categorised into two types: introduced and natural. Introduced complexity is introduced (sheesh) by libraries, frameworks, languages, paradigms, etc. Natural complexity is inherent in the platform itself and is designed to solve fundamental problems of the domain. A good engineer will reduce the introduced complexity and try to accept and deal with the natural complexity. Please stop hiding from natural complexity and start respecting your platform already.

> A quick note on Svelte again: Rich Harris has put out an excellent video on [what's the deal with getters and setters](https://www.youtube.com/watch?v=NR8L5m73dtE&ab_channel=RichHarris) in which he responds to some people's concerns about Svelte's new approach to reactivity. But the only thing that wasn't sufficiently addressed was the «I have to write more code» take.
The ultimate goal is not to write as little code as possible, but to write as little code as is necessary to explicitly describe the intent of your application. If the only thing the technology attracts/offers is "simplicity", then you're trying to sweep important nuances under the rug. You will still encounter them in the future, but from a different angle.

Sorry, but choosing between `onClick={...}`, `on:click={...}` and `@click="..."` is actually a lack of choice. And I'm tired of it.

In some ways, it is understandable why such solutions might have appeared ~decade ago:
* Because the stack is easy to use but difficult to design.
* Because early applications were simpler, and primitive means of templating were enough to support DOM APIs
* Because code performance in this form became sufficient only relatively recently (4-5 years).

And from here comes the main reason:
* Because you need to have a lot of time to experiment and be able to accept new realities and the failure of current approaches.

Unfortunately, people have very little time for the latter. And I don't blame them. We're all like that. But every year the situation becomes more and more sad to me. Don't you yourselves feel sorry for the time spent on HOCs, render-props and other nonsense we threw away months later?

I'm starting to see this as a direct correlation: apps are still being made with difficulty and high financial costs, they're hard to maintain, and instead of learning how to do even one platform right, we're wasting energy on compromises.

## Hijacked syntax

People may complain about the JSX syntax in React, or the template approach in Vue, or the components in Svelte. And with good reason! And not because these solutions are bad, but because they are simply wrong. The difference is huge.

Just let me show you some code examples already, so that you can see what I'm talking about:

**React**
```JSX
function Component() {
  return (
    <div>
      <h1>Hey there</h1>
    </div>
  )
}
```

**Vue**
```html
<template>
  <div>
    <h1>Hey there</h1>
  </div>
</template>
```

**Svelte**
```html
<div>
  <h1>Hey there</h1>
</div>
```

**Angular**
```typescript
@Component({
  selector: 'component',
  template: `
    <div>
      <h1>Hey there</h1>
    </div>
  `,
})
```

Well, actually they look fine. Even Angular, on the whole, doesn't seem like something to shrug off just yet.

But let's try to add some conditional rendering:

**React**
```JSX
function ConditionalComponent({ showMessage }) {
  return (
    <div>
      {showMessage ? (
        <h1>Hey there</h1>
      ) : null}
    </div>
  );
}

...

<ConditionalComponent showMessage={true} />
```

**Vue**
```html
<template>
  <div>
    <h1 v-if="showMessage">Hey there</h1>
  </div>
</template>

<script>
  export default {
    name: 'ConditionalComponent',
    props: {
      showMessage: Boolean
    }
  }
</script>

...

<ConditionalComponent :showMessage="true"
```

**Svelte**
```html
<div>
  {#if showMessage}
    <h1>Hey there</h1>
  {/if}
</div>

...

<ConditionalComponent showMessage={true} />
```

**Angular**
```js
@Component({
  selector: 'conditional-component',
  template: `
    <div>
      <ng-container *ngIf="showMessage">
        <h1>Hey there</h1>
      </ng-container>
    </div>
  `,
})

export class ConditionalComponent {
  @Input() showMessage: boolean = false;
}

...

<conditional-component [showMessage]="true"></conditional-component>
```

Pardon my British, but what the schlocky-donkey rubbish is that?

If-statements inside a view tree with fallback to null (or some plug component, doesn't really matter)? A `v-if` directive? What, a `{#if ...}` templating block? Structural directive with `*ngIf`? Need I say that there is nothing like this in the DOM API and it's all a cheap ass tricks? And I'm not talking about the naming or something. It's about the concept itself.

The one that stood out the most here, of course, was Vue. You either use `v-if` and destroy the components each time over again, or you stupidly hide the component. Using `display: none` in 2023? That's a great way to disrespect your own platform.

But that doesn't mean that Vue is the only one with problems. Other technologies have tried to pull things onto HTML that shouldn't be pulled directly onto it at all.

What abot rendering a list of something? Here it is:

**React**
```typescript
function UserList() {
  return (
    <div>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

**Vue**
```html
<template>
  <div>
    <ul>
      <li v-for="user in users" :key="user.id">{{ user.name }}</li>
    </ul>
  </div>
</template>
```

**Svelte**
```html
<div>
  <ul>
    {#each users as user (user.id)}
      <li>{user.name}</li>
    {/each}
  </ul>
</div>
```

**Angular**
```typescript
@Component({
  selector: 'user-list',
  template: `
    <div>
      <ul>
        <li *ngFor="let user of users; let i = index">{{ user.name }}</li>
      </ul>
    </div>
  `,
})
```

Oh, again. Fictitious syntax, templating, directives. And where is the guarantee that none of this will change in the future? There's no such guarantee. And it has happened before, am I right mr. React and mr. Vue? Where is the guarantee that once such a technology leaves the mainstream, it will not turn into a hard-to-maintain legacy technology? (hello Ember)

Why do you - both users and creators of such solutions - continue to spawn failed technologies that go against the platform idiom? I haven't seen anything like this in mobile development, for example. There, no one is trying to come up with a magic pill; instead, people create convenient APIs to interact with the platform and don't try to hide reality.

## Embrace the DOM API

So, let's move on to possible solutions to this situation. DOM API! There's a lot of interesting things about it; oddly enough, people have worked on it meticulously over the years. It's incredibly huge, it has a lot of features, and there are a number of such features that you can't hide from behind a wall of props.

How would we solve the problem with conditional rendering, for example? Well, DOM API have [node.append()](https://developer.mozilla.org/en-US/docs/Web/API/Element/append) or [node.appendChild()](https://developer.mozilla.org/en-US/docs/Web/API/Node/appendChild) methods, [node.remove()](https://developer.mozilla.org/en-US/docs/Web/API/Element/remove) method and [node.isConnected](https://developer.mozilla.org/en-US/docs/Web/API/Node/isConnected) property. We can use it to add or remove a node at any time and determine if it is connected to the DOM tree. React, for example, basically does just that.

But here, too, they were reckless in taking this description outside of the component. But does it make any sense? The state of a node (even with its children) connected to a DOM tree should be reported by the component itself, not by any external blocks. So we could do something like that:

```typescript
export function Component({ showMessage }) {
  h('div', () => {
    h('h1', {
      text: 'Hey there',
      visible: showMessage,
    })
  })
}
```

No made-up syntaxes or extensions, no attempts to hide the essentials behind a wall of magic. Just a regular JS function with a convenient API for interacting with the DOM. How can we use this component in our application? As a plain function again, of course:

```typescript
using(body, () => {
  Component({ showMessage: true })
})
```

This approach borrows ideas from SwiftUI and Flutter. Where the second callback argument is an alternative to SwiftUI's blocks for nesting components, and the `visible` property is an analogue of the `visible` property from Flutter as is. And note that `visible` here is not a "hack" as in Vue, but an actual insertion/extraction of a DOM node from a subtree.

And to do so, we didn't need to invent some abstract syntax to mimic the behaviour we needed. Yes, I realize that you may not like javascript, and you may have your reasons. But the "native" language of the frontend is still javascript, and trying to get around it with fake solutions only makes things worse. We've seen it many times before.

Okay, the approach with `visible` is somewhat clear. What about the rendering the list of components? Here it is:

```typescript
export const function User({ key, text, isRestricted}) {
  h('li', {
    attr: { id: key },
    text: text,
    visible: isRestricted,
    classList: ["border-gray-200"]
  })
}

using(document.body, () => {
  h('ul', () => {
    list(users, ({ store: user, key: idx }), () => {
      User({ key: idx, text: user.name, isDeleted: user.isRestricted })
    })
  })
})
```

This approach partially replicates the one presented in SwiftUI with their:

```swift
List(users) { user in
  // usage of user
}
```

In addition, each of the variables or properties used in the code presented can be reactive. This way, every time we have a change in the list of users or their attributes, it will be reflected in the final layout.

Also, this `list` approach renders the list a bit more interesting than it might seem at first glance. Instead of computing the entire contents of each item in a list, templates (js templates, not to be confused with templates from Vue and others) are created for the application to work with, and the templates are generated in advance, one for each `list` call. Thus, for each change in the reactive value of `users`, we only need to create a new instance of the already configured template, instead of calculating everything in runtime.

But what about event handlers and attributes specification? Well, we can imagine something like the following:

```typescript
using(document.body, () => {
  h('section', () => {
    spec({ style: {width: '15em'} })

    h('form', () => {
      spec({
        handler: {
          config: { prevent: true },
          on: { submit },
        },
        style: {
          display: 'flex',
          flexDirection: 'column',
        },
      })

      h('input', {
        attr: { placeholder: 'Username' },
        handler: { input: changeUsername },
      })

      h('input', {
        attr: { type: 'password', placeholder: 'Password' },
        classList: ['w-full', 'py-2', 'px-4'],
        handler: { input: changePassword },
      })

      h('button', {
        text: 'Submit',
        attr: {
          disabled: fields.map(
            fields => !(fields.username && fields.password),
          ),
        },
      })
    })
  })
})
```

Here `changeUsername` and `changePassword` are events to reactively change perspective values. And `fields` is a reactive object with the attributes themselves. Yes, a store. Like it or not.
Using the `map()` method, we can create a derived property (hello `$derived` from Svelte) that will update when the username or password is updated and change the attribute of the button.

I know what you might think looking at it for the first time:
* It looks very unusual
* Hella verbose
* Having to work with DOM API nuances on your own

But is this really the case?

Nothing unusual here, it's just a JS functions. And the rest:
* `attr` - object with node's attributes.
* `style` - object with node's styles.
* `classList` - an array of node classes. By the way, this is [what it is called in the DOM API itself](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList)
* `handler` - object with node's event handlers with configuration object inside (note `config: { prevent: true }`)
* `spec` - is just a wrapper function to describe the property categories of a node, if the component has child elements inside it's callback. This way you can describe the set of properties at the top level of component (actually anywhere inside the callback, but it's not that important.)

Verbose? Yes, I agree. This approach looks more verbose than what React, Vue, Svelte, Solid and others offers. But the solution presented above doesn't fool you about the complexity of the frontend, nor does it offer a magic pill of "simplicity." Instead, you need to face reality head-on and learn to make friends with it, not fight it. In this way, you will get a clear picture of how exactly your application is built. Yes, it's verbose. But does it look very complicated? Or so complex that you can't understand what's going on here? I bet you understand exactly what each of these lines is doing.

Moreover, you don't need to work directly with the DOM API. All you really need is a convenient JS API to interact with it. I am sure that the view tree should be managed by native tools. And manual work on adding, removing, updating the tree? Yes, this one can be put under the hood of some technologies.

Once again, I'm not trying to sell you some new blazingly kool-aid nuts technology. Instead, I just want to point out the problems of existing solutions and how they can be solved by native tools without reinventing the wheel.

I'll say it again - start respecting your own platform. Everyone has learned to work with their platform, unlike the frontend developers who try to cover themselves with yet another bogus solution year after year.

## Everything's fine until it's not?

Fair question. Showing simple examples is difficult to convey the real picture, as some problems may not show up in simple examples.


Here's how, for example, you might describe a form section in a real application:

```typescript
export const Auth = () => {
  h("div", () => {
    spec({
      classList: ["mt-10", "max-w-sm", "w-full"],
    });

    h("form", () => {
      Input({
        type: "email",
        label: "Email",
        inputChanged: authForm.fields.email.changed,
        errorText: authForm.fields.email.$errorText,
        errorVisible: authForm.fields.email.$errors.map(Boolean),
      });

      Input({
        type: "password",
        label: "Password",
        inputChanged: authForm.fields.password.changed,
        errorText: authForm.fields.password.$errorText,
        errorVisible: authForm.fields.password.$errors.map(Boolean),
      });

      Button({
        text: "Create",
        event: authForm.submit,
        size: "base",
        prevent: true,
        variant: "default",
      });

      ErrorHint($authError, $authError.map(Boolean));
    });
  });
};

...

export const Input = ({
  value,
  type,
  label,
  required,
  inputChanged,
  errorVisible,
  errorText,
}: {
  value?: Store<string>;
  type: string;
  label: string;
  required?: boolean;
  inputChanged: Event<any>;
  errorVisible?: Store<boolean>;
  errorText?: Store<string>;
}) => {
  h("div", () => {
    spec({
      classList: ["mb-6"],
    });

    h("label", () => {
      spec({
        classList: ["block", "mb-2", "text-sm", "font-medium", "text-gray-900", "dark:text-white"],
        text: label,
      });
    });

    h("input", () => {
      const localInputChanged = createEvent<any>();
      sample({
        source: localInputChanged,
        fn: (event) => event.target.value,
        target: inputChanged,
      });

      spec({
        classList: [
          "bg-gray-50",
          "border",
          "border-gray-300",
          "text-gray-900",
          "text-sm",
          "rounded-lg",
          "focus:ring-blue-500",
          "focus:border-blue-500",
          "block",
          "w-full",
          "p-2.5",
          "dark:bg-gray-700",
          "dark:border-gray-600",
          "dark:placeholder-gray-400",
          "dark:text-white",
          "dark:focus:ring-blue-500",
          "dark:focus:border-blue-500",
        ],
        attr: { type: type, required: Boolean(required), value: value || createStore("") },
        handler: { on: { input: localInputChanged } },
      });
    });

    ErrorHint(errorText, errorVisible);
  });
};

...

export const ErrorHint = (text: Store<string> | string | undefined, visible: Store<boolean> | undefined) => {
  h("p", {
    classList: ["mt-2", "text-sm", "text-red-600", "dark:text-red-400"],
    visible: visible || createStore(false),
    text: text || createStore(""),
  });
};

```

What about describing some list of logs using predefined card with tags, attributes and dynamic content?

```typescript
export const LogsList = () => {
  h("div", () => {
    spec({
      classList: ["flex", "flex-col", "space-y-6", "mt-2"],
    });

    list(logModel.$logsGroups, ({ store: group }) => {
      CardHeaded({
        tags: group.map((g) => g.tags),
        href: group.map((g) => `${g.schema_name}/${g.group_hash}`),
        content: () => {
          LogsTable(group.map((g) => g.logs));
        },
        withMore: true,
      });
    });
  });
};
```

You don't need to reason about all these `createStore`, `createEvent`. Store is just a reactive value, and event is just a signal to change them or invoke execution of some effect. They can be from any library.

What is important here is the very fact of describing the view, the view logic. I believe that even less trivial descriptions of view should not require extraneous solutions. And, again, this is not an advertisement for some other solution (although such a library exists, but you won't find it anyway). Instead, I'm just trying to get you to think. Are the existing solutions doing their job optimally? No? Are you sure why **exactly** not?


## Why it's important

You may think I'm being too aggressive with current mainstream solutions. But that's not true.

In fact, I believe that each of these technologies is needed to some degree. Or was needed. At least for general frontend development. But what I don't like is that we're literally stuck in the past decade, and no one in the mainstream is trying to bring this to the attention of developers. As a result, our applications still have zero reproducibility, and the labor intensity of the tasks, even for monotonic ones, is featural.

And instead of recognizing the failure of current practices, we continue to choose between different colors of the same felt-tip pen.

Respect your platform, please.