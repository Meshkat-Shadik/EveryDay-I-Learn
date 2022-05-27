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
'books': instance.books?.map((e) => e?.toJson())?.toList(),,
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

## Date: 25 February, 2022 (Flutter Stream)

> ### BlocBuilder, BlocListener & BlocConsumer

- `BlocConsumer` : Consists of BlocBuilder and BlocListener

```dart
BlocConsumer<ConnectivityCubit, bool>(
      listener: (context, state) {
        state
            ? context.router.replace(const SplashPageRoute())
            : const Text("Doesn't show this");
      },
      //shows this Scaffold
      builder: (context, state) => const Scaffold(
        body: Center(
          child: InternetDisablePage(),
        ),
      ),
    );
```

- `BlocBuilder` : Altime listens and builds the widget

```dart
BlocBuilder<ConnectivityCubit, bool>(
          builder: (context, state) {
            return state? NormalPage():InternetDisablePage();
          }
);
```

- `BlocListener` : Listens and fire events not build the widget

```dart
BlocListener<AuthBloc, AuthState>(
      listener: (context, state) {
        state.map(
          initial: (_) {},
          authenticated: (_) {
            context.router.replace(const NotesOverviewPageRoute());
          },
          unAuthenticated: (_) =>
              context.router.replace(const SignInPageRoute()),
        );
      },
```

## Date: 4 March, 2022 (Flutter Internationalization/Localization)

> ### Usage of Easy Localization

- Dependency adding to **pubspec.yaml**

```yaml
dependencies:
  easy_localization: ^3.0.0
```

- Create folder in project like this structure

```
assets
└── translations
    ├── en.json
    └── bn.json
```

- Add the assests to the **pubspec.yaml**

```yaml
flutter:
  assets:
    - assets/translations/
```

- Download i18n Manager
  https://www.electronjs.org/apps/i18n-manager

work like the picture below.
![image](https://user-images.githubusercontent.com/31488481/156725126-3cc206cd-9668-4a6a-b220-60ff40b5b931.png)

- Add some code in **main.dart**

```dart
  WidgetsFlutterBinding.ensureInitialized();
  await EasyLocalization.ensureInitialized();
  runApp(
    EasyLocalization(
        path: 'assets/translations',
        supportedLocales: const [
          Locale('en'),
          Locale('bn'),
        ],
        fallbackLocale: const Locale('en'),
        child: const MyApp()),
  );
```

- Add some more code to MaterialApp

```dart
 return MaterialApp(
      localizationsDelegates: context.localizationDelegates,
      supportedLocales: context.supportedLocales,
      locale: context.locale,
      home: const MyHomePage(),
    );
```

- Generator

```sh
flutter pub run easy_localization:generate -h

flutter pub run easy_localization:generate -S "assets/translations" -O "lib/translations"

flutter pub run easy_localization:generate -S "assets/translations" -O "lib/translations" -o "locale_keys.g.dart" -f keys
```

- Add Generated CodegenLoader() inside **RunApp's EasyLocalization**

```dart
assetLoader: const CodegenLoader(),
```

- Use in presentation

```dart
import 'package:easy_localization/easy_localization.dart'; //for use tr()
 LocaleKeys.yourKeyName.tr(),
```

- Change the Locale

```dart
context.setLocale(const Locale('en'));
```

## Date: 10 March, 2022 (Flutter - Bottom Bar )

> ### We want to make this type of UI
>
> ![image](https://user-images.githubusercontent.com/31488481/157603507-0170d233-d9b7-4499-9419-fe3a23c3d6e3.png)

You can use different ways to implement this UI.

\_Previously I used **Stack\_**

- Easy implementation with Expanded.

```dart
 Column(
          children: <Widget>[
            Expanded(
              child: ListView(),
            ),
            Container(
              height: 50,
              width: MediaQuery.of(context).size.width,
              color: Colors.blue,
            ),
          ],
        ),
```

## Date: 17 March, 2022 (Dart - Immutability)

> Source

https://medium.flutterdevs.com/explore-immutable-data-structures-in-dart-flutter-86c350b7d014#:~:text=In%20object%2Doriented%20and%20functional,adjusted%20after%20it%20is%20made.

> In DDD

We should use @immutable at the **_domain/core/value_objects_** as this should not be changeable.


## Date: 27 May. 2022 (Node.js - Axios multiple request)
```js
const axios = require("axios");

const requestAPI = async(req, res, next) => {
    console.log("request API Called");
    const names = req.query.name.split(",");
    req.names = names;
    next();
};

const requestOne = async(req, res) => {
    console.log("Req ashtese = " + req.names);

    let reqArr = [];

    for (let i = 0; i < req.names.length; i++) {
        reqArr.push(
            axios.get("https://jsonplaceholder.typicode.com/posts/" + req.names[i])
        );
        // console.log(req.names[i]);
    }

    for (let i = 0; i < reqArr.length; i++) {
        //console.log(reqArr[i]);
    }

    axios
        .all(reqArr.map((e) => e))
        .then(
            axios.spread((...responses) => {
                const responseOne = responses[0];
                const responseTwo = responses[1];
                const responesThree = responses[2];
                // use/access the result
                console.log(responseOne.data);
                console.log(responseTwo.data);
            })
        )
        .catch((errors) => {
            // react on errors.
        });
};
```

