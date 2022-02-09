# Date: 9 February, 2022 (Flutter)

> ## Passing BlocProvider inside Navigator

`CurrentBloc -> main(BlocProvider(FirstPage)) -> FirstPage() -> NextPage()`

In this condition NextPage is not wrapped with BlocProvider, and we pass the currentBlocProvider to the next like this.

```dart
Navigator.of(context).push(MaterialPageRoute(
              builder: (_) => BlocProvider.value(
                value: BlocProvider.of<CurrentBloc>(context),
                child: NextPage(props),
              ),
            ));
```

# Date: 10 February, 2022 (Flutter)

> ## Riverpod ref.listen based snackbar

```dart
 ref.listen<State>(responseStateNotifierProvider,
        (previous, current) {
      current.maybeWhen(
        success: (d) => ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(
            content: current.maybeWhen(
              success: (d) {
                return Text(d.toString());
              },
              orElse: () => Container(),
            ),
            backgroundColor: current.maybeWhen(
              success: (d) {
                if (d.contains("Can't")) {
                  return Colors.red;
                } else {
                  return Colors.green;
                }
              },
              orElse: () => Colors.grey,
            ),
          ),
        ),
        orElse: () {},
      );
```

> ## JSON Serialized **g.dart** list modifying
>
> Actually, for a list returning is not mapped in **g.dart** file. In case, we have to write list manually.

`Previously`

```dart
 'books': instance.books
```

`Modified`

```dart
'books': instance.books?.map((e) => e.toJson()).toList(),
```

_Tips: Also look out (Debug) the *ENUM* value wheather it is returning properly or not._
