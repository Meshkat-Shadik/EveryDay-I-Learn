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
