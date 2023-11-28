![Banner](https://github.com/41y08h/fquery/blob/main/media/Banner.png?raw=true)

⚡Are you ready to supercharge your Flutter app development?

Introducing fquery - an easy-to-use, yet efficient and reliable asynchronous state management solution for Flutter! It effortlessly caches, updates, and fully manages asynchronous data in your Flutter apps.

With this powerful tool at your disposal, managing server state (REST API, GraphQL, etc), local databases like SQLite, or anything async has never been easier. Just provide a `Future` and watch the magic unfold.

## Trusted & Used by

### UC San Diego

![UC San Diego](https://github.com/41y08h/fquery/blob/main/media/ucsd-banner.png?raw=true)

The University of California, San Diego has shifted to [fquery](https://github.com/41y08h/fquery/) as the backbone of their [mobile application](https://mobile.ucsd.edu/), which has over 30,000 users and serves as the app used by the generations of students.
With fquery's efficient and easy-to-use async state management, the developers are enjoying a comfort of seamless state management with refactoring even files with 200 lines to just 20 lines. They also noticed a significant reduction in the hot reload.

All of this only to have more time, and easy-to-manage structure to develop the features that matter the most. They are confident that the codebase will continue to be managable, and provide the team with a better structure.

### Stargazers and others

![GitHub Repo stars](https://img.shields.io/github/stars/41y08h/fquery?style=social)

The project's growth has almost been completely organic, it has grown popular in the developer community and is growing by the day, consider starring it if you've found it useful.

As a developer, you too can leverage the power of this tool to create a high-quality mobile application that provides an exceptional user experience. [fquery](https://github.com/41y08h/fquery/) is a reliable and efficient solution that has already been proven successful in UC San Diego's app. So, why not choose it for your next project and take advantage of its powerful features to deliver a seamless experience to your users?

## 🌌 Features

<!-- Non-technical features -->

- Easy to use
- Powerful

<!-- Technical features -->

- Fully customizable
- No boilerplate code and easy-to-use
- Data fetching logic agnostic
- Automatic caching
- Garbage collection
- Automatic re-fetching of stale data
- State data invalidation
- Manual updates
- Dependent queries
- Parallel queries

## ❔Problem definition

Let me ask you a simple question, **How do you manage server state in your Flutter apps?** Majority developers will answer that they use Riverpod, Bloc, `FutureBuilder`, or any other general-purpose state management solution. This usually results in writing a lot of boilerplate code and repeating data fetching, caching, and other logic over and over again.

The thing is, existing state management solutions are very general and are suited for anything that's a global state in your app _and hence the term "general"_, but do not work great when used for asynchronous states like server state, this is because server state is way too different. The server state is -

- Asynchronous state and requires asynchronous APIs for fetching and updating)
- Stored in a remote location and _can be changed without your knowledge, from just anywhere in the world_ and **this alone means a lot, staying synchronized with the data and making sure that it is not stale**

### How does FQuery tackle this problem?

FQuery is powered by [flutter_hooks](https://pub.dev/packages/flutter_hooks). It is very similar to [swr](https://github.com/vercel/swr) and [react-query](https://github.com/tanstack/query). It provides you with easy-to-use hooks. Just tell it where to get the data by giving it a `Future` and the rest is automatic. It can be fully configured to match your needs, you can configure each and everything.

## 📄 Example

Here's a very simple widget that makes use of the `useQuery` hook:

```dart
class Posts extends HookWidget {
  const Posts({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final posts = useQuery(['posts'], getPosts);

    return Builder(
      builder: (context) {
        if (posts.isLoading) {
          return const Center(child: CircularProgressIndicator());
        }

        if (posts.isError) {
          return Center(child: Text(posts.error!.toString()));
        }

        return ListView.builder(
          itemCount: posts.data!.length,
          itemBuilder: (context, index) {
            final post = posts.data![index];
            return ListTile(
              title: Text(post.title),
            );
          },
        );
      },
    );
  }
}
```

## 🧑‍💻 Usage

You'll need to install [flutter_hooks](https://pub.dev/packages/flutter_hooks) before you can start using this library. You'll need to wrap
your entire app inside a `QueryClientProvider` and you are good to go.

```dart
void main() {
  runApp(
    QueryClientProvider(
      queryClient: queryClient,
      child: CupertinoApp(
```

### Queries

To query data in your widgets, you'll need to extend the widget using `HookWidget` or `StatefulHookWidget` (for stateful widgets). These classes are exported from the [flutter_hooks](https://pub.dev/packages/flutter_hooks) package.

A query instance is a subscription to asynchronous data stored in the cache. Every query needs -

- A **Query key**, uniquely identifies the query stored in the cache.
- A `Future` that either resolves or throws an error

The same query key can be used in multiple instances of the `useQuery` hook and the data will be shared throughout the app.

```dart
Future<List<Post>> getPosts() async {
  final res = await Dio().get('https://jsonplaceholder.typicode.com/posts');
  return (res.data as List)
      .map((e) => Post.fromJson(e as Map<String, dynamic>))
      .toList();
}

class Posts extends HookWidget {
  const Posts({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final posts = useQuery(['posts'], getPosts);
```

The returned value of the `useQuery` hook is an instance of `UseQueryResult` and contains all the information related to that query. A `Builder` widget comes in handy when rendering the results.

```dart
// The query has no data to display
if (posts.isLoading) {
  return const Center(child: CircularProgressIndicator());
}

// An error has occurred
if (posts.isError) {
  return Center(child: Text(posts.error!.toString()));
}

// Success, data is ready to display
return ListView.builder(
  itemCount: posts.data!.length,
  itemBuilder: (context, index) {
    final post = posts.data![index];
    return ListTile(
      title: Text(post.title),
    );
  },
);
```

### Query configuration

A query is fully customizable to match your needs, these configurations can be passed as named parameters into the `useQuery` hook

```dart
// These are default configurations
final posts = useQuery(
  ['posts'],
  getPosts,
  enabled: true,
  cacheDuration: const Duration(minutes: 5),
  refetchInterval: null // The query will not re-fetch by default,
  refetchOnMount: RefetchOnMount.stale,
  staleDuration: const Duration(seconds: 10),
);
```

- `enabled` - specifies if the query fetcher function is automatically called when the widget renders and can be used for _dependent queries_.
- `cacheDuration` - specifies the duration unused/inactive cache data remains in memory; the cached data will be garbage collected after this duration. The longest duration will be used when different values are specified in multiple instances of the query.
- `refetchInterval` - specifies the time interval in which all queries will re-fetch the data, setting it to `null` (default) will turn off re-fetching.
- `refetchOnMount` - specifies the behavior of the query instance when the widget is first built and the data is already available.
  - `RefetchOnMount.always` - will always refetch when the widget is built.
  - `RefetchOnMount.stale` - will fetch the data if it is stale (see `staleDuration`).
  - `RefetchOnMount.never` - will never refetch.
- `staleDuration` - specifies the duration until the data becomes stale. This value applies to each query instance individually.

### Query invalidation

This technique can be used to manually mark the cached data as stale and potentially even re-fetch them. This is especially useful when you know that the data has been changed. `QueryClient` (see below) has an `invalidateQueries()` method that allows you to do that. **You can make use of the `useQueryClient` hook to obtain the instance of `QueryClient`** that you passed with `QueryClientProvider`.

```dart
final queryClient = useQueryClient();

// Invalidate every query with a key that starts with `post`
queryClient.invalidateQueries(['posts']);

// Both queries will be invalidated
final posts = useQuery(['posts'], getPosts);
final post = useQuery(['posts', 1], getPosts);

// Use `exact: true` to exactly match the query
queryClient.invalidateQueries(['posts'], exact: true);

// Only this will invalidate
final posts = useQuery(['posts'], getPosts);
```

When a query is invalidated, two things will happen:

- It marks it as stale and this overrides any `staleDuration` configuration passed to `useQuery`.
- If the query is being used in a widget, it will be re-fetched, otherwise, it will be re-fetched when it is used by a widget at a later point in time.

### Manual updates

You probably already know how the data is changed and don't want to re-fetch the whole data again. You can set it manually using the `setQueryData()` method on the `QueryClient`. It takes a query key and an updater function. If the query data doesn't exist already in the cache (that's why `previous` is nullable), it'll be created.

```dart
final queryClient = useQueryClient();

// The `Type` of returned data must match the `Type` of data
// stored in the cache, otherwise an error will be thrown
queryClient.setQueryData<List<Post>>(['posts'], (previous) {
  return previous?.map((post) {
    return post.copyWith(
      title: "lorem ipsum"
    );
  }).toList() ?? <Post>[]
})
```

### QueryClient

A `QueryClient` is used to interact with the query cache. It is made available throughout the app using a `QueryClientProvider`. **It can be configured to change the default configurations of the queries.**

```dart
final queryClient = QueryClient(
  defaultQueryOptions: DefaultQueryOptions(
    cacheDuration: Duration(minutes: 20),
    refetchInterval: Duration(minutes: 5),
    refetchOnMount: RefetchOnMount.always,
    staleDuration: Duration(minutes: 3),
  ),
);

void main() {
  runApp(
    QueryClientProvider(
      queryClient: queryClient,
      child: CupertinoApp(
```

## Contributing

If you've ever wanted to contribute to open source, and a great cause, now is your chance ✨, feel free to open an issue or submit a PR at the [GitHub repo](https://github.com/41y08h/fquery). See [Contribution guide](CONTRIBUTING.md) for more details.

## Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->
