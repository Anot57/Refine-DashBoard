---
id: add-show-page
title: 3. Adding Show Page
tutorial:
    order: 0
    prev: tutorial/adding-crud-pages/{preferredUI}/add-edit-page
    next: tutorial/adding-crud-pages/{preferredUI}/add-create-page
---

import SharedComponents from "../../../partials/tutorial/headless-layout.md";

<SharedComponents />`

## Creating the Show Page

First, we need to create our file, named `show.tsx`, under the `src/pages/blog-posts` folder. We will then copy the show page code generated by Inferencer and paste it into the file; for this, follow these steps:

1. Navigate to <a href="http://localhost:3000/blog-posts" rel="noopener noreferrer nofollow">localhost:3000/blog-posts</a> in your browser.

2. Click on any of the "Show" buttons in the "Actions" column of the table to open the edit page.

3. Click on the "Show Code" button in the bottom right corner of the page.

4. You can see the show page code generated by Inferencer. Copy it by clicking on the "Copy" button.

5. Paste the code into the newly created `show.tsx` file.

You can see an example show page generated by Inferencer below:

```tsx live previewOnly previewHeight=600px url=http://localhost:3000/blog-posts/show/123
setInitialRoutes(["/blog-posts/show/123"]);

import { Refine } from "@refinedev/core";
import { HeadlessInferencer } from "@refinedev/inferencer/headless";
import routerBindings, {
    NavigateToResource,
    UnsavedChangesNotifier,
} from "@refinedev/react-router-v6";
import dataProvider from "@refinedev/simple-rest";
import { BrowserRouter, Route, Routes, Outlet } from "react-router-dom";

const App = () => {
    return (
        <BrowserRouter>
            <Refine
                routerProvider={routerBindings}
                dataProvider={dataProvider("https://api.fake-rest.refine.dev")}
                resources={[
                    {
                        name: "blog_posts",
                        list: "/blog-posts",
                        show: "/blog-posts/show/:id",
                        create: "/blog-posts/create",
                        edit: "/blog-posts/edit/:id",
                    },
                ]}
                options={{
                    syncWithLocation: true,
                    warnWhenUnsavedChanges: true,
                }}
            >
                <Routes>
                    <Route
                        element={
                            <Layout>
                                <Outlet />
                            </Layout>
                        }
                    >
                        <Route
                            index
                            element={
                                <NavigateToResource resource="blog_posts" />
                            }
                        />

                        <Route path="/blog-posts">
                            <Route index element={<HeadlessInferencer />} />
                            <Route
                                path="show/:id"
                                element={<HeadlessInferencer />}
                            />
                            <Route
                                path="edit/:id"
                                element={<HeadlessInferencer />}
                            />
                            <Route
                                path="create"
                                element={<HeadlessInferencer />}
                            />
                        </Route>

                        <Route path="*" element={<div>Error!</div>} />
                    </Route>
                </Routes>

                <UnsavedChangesNotifier />
            </Refine>
        </BrowserRouter>
    );
};

render(<App />);
```

## Understanding the Show Component

### Hooks and Components in Show Page

-   The `useShow` hook is used to get single record data by using the `id` in the URL. It sends the parameters to the `dataProvider`'s `getOne` function and returns the result.

-   The `useNavigation` hook is used for navigating between pages. In this case, we are using it to navigate to the `edit` and `list` pages when the user clicks on the "Blog Posts List" buttons.

-   The `useResource` is hook is used to get current resource information and `resources` that are defined in `<Refine/>`.

    > For more information, refer to the [`useShow`](/docs/api-reference/core/hooks/show/useShow/),[`useNavigation`](/docs/api-reference/core/hooks/navigation/useNavigation/) and [`useResource` documentations&#8594](/docs/api-reference/core/hooks/resource/useResource/)

### Handling Relationships

On the show page, we have a single record, and it may have relationships with other resources.

For example, the `blog_posts` resource has a relationship with the `categories` resource. In this case, we can use the `useOne` hook provided by **refine**, which allows us to fetch single record data by using the `id` and `resource` parameters.

In the auto-generated show page code, Inferencer used the `useOne` hook to fetch the category data of the blog post record like below:

```tsx
const { data: categoryData, isLoading: categoryIsLoading } = useOne({
    resource: "categories",
    id: record?.category?.id || "",
});
```

To ensure that the related data is fetched only after the blog post record has been successfully retrieved, we can use the `queryOptions` object. By setting the `enabled` property to `true` only if the record variable is truthy, we can control when the related data is fetched, just like below:

```tsx
const { data: categoryData, isLoading: categoryIsLoading } = useOne({
    resource: "categories",
    id: record?.category?.id || "",
    queryOptions: {
        enabled: !!record,
    },
});
```

> For more information, refer to the [`useOne` documentation&#8594](/docs/api-reference/core/hooks/data/useOne/)

## Adding the Show Page to the App

Now that we have created the show page, we can add it to the `App.tsx` file:

1. Open `src/App.tsx` file on your editor.

2. Import the created `BlogPostShow` component.

3. Replace the `HeadlessInferencer` component with the `BlogPostShow` component.

```tsx title="src/App.tsx"
import { Refine } from "@refinedev/core";
import { HeadlessInferencer } from "@refinedev/inferencer/headless";
import routerBindings, {
    NavigateToResource,
    UnsavedChangesNotifier,
} from "@refinedev/react-router-v6";
import dataProvider from "@refinedev/simple-rest";
import { BrowserRouter, Route, Routes, Outlet } from "react-router-dom";

import { BlogPostEdit } from "pages/blog-posts/edit";
import { BlogPostList } from "pages/blog-posts/list";
//highlight-next-line
import { BlogPostShow } from "pages/blog-posts/show";

import { Layout } from "components/layout";

import "./App.css";

const App = () => {
    return (
        <BrowserRouter>
            <Refine
                routerProvider={routerBindings}
                dataProvider={dataProvider("https://api.fake-rest.refine.dev")}
                resources={[
                    {
                        name: "blog_posts",
                        list: "/blog-posts",
                        // highlight-next-line
                        show: "/blog-posts/show/:id",
                        create: "/blog-posts/create",
                        edit: "/blog-posts/edit/:id",
                    },
                ]}
                options={{
                    syncWithLocation: true,
                    warnWhenUnsavedChanges: true,
                }}
            >
                <Routes>
                    <Route
                        element={
                            <Layout>
                                <Outlet />
                            </Layout>
                        }
                    >
                        <Route
                            index
                            element={
                                <NavigateToResource resource="blog_posts" />
                            }
                        />

                        <Route path="blog-posts">
                            <Route index element={<BlogPostList />} />
                            {/* highlight-next-line */}
                            <Route path="show/:id" element={<BlogPostShow />} />
                            <Route path="edit/:id" element={<BlogPostEdit />} />
                            <Route
                                path="create"
                                element={<HeadlessInferencer />}
                            />
                        </Route>

                        <Route path="*" element={<div>Error!</div>} />
                    </Route>
                </Routes>
                <UnsavedChangesNotifier />
            </Refine>
        </BrowserRouter>
    );
};
export default App;
```

Now, we can see the show page in the browser at <a href="http://localhost:3000/blog-posts/show/123" rel="noopener noreferrer nofollow">localhost:3000/blog-posts/show/123</a>

<br/>

<Checklist>

<ChecklistItem id="add-show-page-headless">
I have added the show page to the app.
</ChecklistItem>
<ChecklistItem id="add-show-page-headless-2">
I understood the show page components and hooks.
</ChecklistItem>
<ChecklistItem id="add-show-page-headless-3">
I understood the relationship handling.
</ChecklistItem>

</Checklist>