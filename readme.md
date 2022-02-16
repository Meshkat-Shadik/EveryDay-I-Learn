## Date: 9 February, 2022 (Flutter)

> ### Passing BlocProvider inside Navigator

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

## Date: 10 February, 2022 (Flutter)

> ### Riverpod ref.listen based snackbar

```dart
 ref.listen<State>(responseStateNotifierProvider,
        (previous, current) {
      current.maybeWhen(
        success: (d) => ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(
            content: Text(d),
            backgroundColor: d.contains("Can't")?Colors.red:Colors.green,
          ),
        ),
        orElse: () {},
      );
```

> ### JSON Serialized **g.dart** list modifying
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

## Date: 11 February, 2022 (Prisma+Node.js)

> ### Grab auto-completion in prisma

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

## Date: 12 February, 2022 (Flutter + GraphQL)

> ### GraphQL + Flutter CRUD
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

## Date: 13 February, 2022 (Flutter)

> ### Injectable with Flutter
>
> Some rules,

- @module - dependency
- @lazySingleton - dependency
- @LazySingleton(as: IAuthFacade) - repository/facade
- @injectable - bloc(application)

## Date: 14 February, 2022 (Flutter)

> ### Dartz OptionOf() and some() difference

`some`

```dart
 emit(state.copyWith(
        isSubmitting: false,
        authFailureOrSuccessOption:
            some(failureOrSuccess),
      ));
```

`OptionOf`

```dart
some(x == null? none():some(x));

//instead of we can write
optionOf(x) //automates the null checking operation
```

## Date: 15 February, 2022 (Flutter Stream)

> ### Stream & StreamSubscription
>
> Stream subscription bloc uses **_event_** instead of returning **_state_**.

Then we have to watch/fire the event

`implementation on bloc (Application Layer)`

```dart
    on<EventName>((event, emit) async {
      await _noteStreamSubscription?.cancel();
      emit(const State.loading());
      _noteStreamSubscription =
          repo.watchAll().listen((eitherValue) {
        return add(Event.successEvent(eitherValue));
      });
    });
```

`Calling the event coming from stream`

```dart
    on<SuccessEvent>((event, emit) async {
      emit(event.eitherValue.fold(
        (l) => State.loadFailure(l),
        (r) => State.loadSucess(r),
      ));
    });
```

`implementation on repository (Infrastructure Layer)`

```dart
Stream<Either<NoteFailure, KtList<Note>>> watchAll() async* {
    final userDoc = await _firestore.userDocument();
    yield* userDoc.noteCollection
        .orderBy('serverTimeStamp', descending: true)
        .snapshots()
        .map(
          (snapshot) => right<NoteFailure, KtList<Note>>(
            snapshot.docs.map((doc) {
              return NoteDto.fromFirestore(doc).toDomain();
            }).toImmutableList(),
          ),
        )
        .onErrorReturnWith((e, stackTrace) {
      if (e is FirebaseException && e.message!.contains('permission-denied')) {
        return left(const NoteFailure.insufficientPermission());
      } else {
        return left(const NoteFailure.unexpected());
      }
    });
  }
```
