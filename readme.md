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

# Date: 11 February, 2022 (Prisma+Node.js)

> ## Grab auto-completion in prisma

To show the auto completion, which is the most helping hand to run with **Prisma**.

**\_Just put await before it, and for using **await** we have to wrap the function with \_async\_\_**

```js
const getStudentById = async (req, res, next) => {
  try {
    const students = await prisma.student.findUnique({
      where: {
        roll: Number(req.query.roll),
      },
      include: { teacher: true },
    });
    res.status(200).json(students);
  } catch (error) {
    next(error);
  }
};
```

# Date: 12 February, 2022 (Flutter + GraphQL)

> ## GraphQL + Flutter CRUD
>
> Source:
> https://github.com/Mahmudul-Sumoon/GraphQL-Riverpod by Me and @Mahmudul-Sumoon

Problem we have faced, we can't be able to watch updated value in read after mutating datas.

`Previously`

```dart
  @override
  Future<BooksModel> getAllBooks() async {
    QueryResult result = await _client.value.query(
      QueryOptions(
        document: gql(GetData.getAllBooks),
      ),
    );
    if (result.hasException) {
      return BooksModel.fromJson(result.data!);
    } else {
      return BooksModel.fromJson(result.data!);
    }
  }
```

`Updated`

```dart
  @override
  Future<BooksModel> getAllBooks() async {
    QueryResult result = await _client.value.query(
      WatchQueryOptions(
        document: gql(GetData.getAllBooks),
      ),
    );
    if (result.hasException) {
      return BooksModel.fromJson(result.data!);
    } else {
      return BooksModel.fromJson(result.data!);
    }
  }
```
