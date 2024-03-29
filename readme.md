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

We should use **_@immutable_** at the **_domain/core/value_objects_** as this should not be changeable.

## Date: 27 May. 2022 (Node.js - Axios multiple request)

```js
const axios = require("axios");

const requestAPI = async (req, res, next) => {
  console.log("request API Called");
  const names = req.query.name.split(",");
  req.names = names;
  next();
};

const requestOne = async (req, res) => {
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

## Date: 17 June, 2022 (Stack Clipped Area OnTap)

> Problem and Solution

https://stackoverflow.com/questions/70928944/flutter-stack-clip-not-detecting-gestures

```dart
Stack(
      clipBehavior: Clip.none,
      children: [
        //first child wrapping with column
        Column(
          children: [
            Container(),
          ],
        ),
        //adding a sizedbox
        SizedBox(height: 180),

        //add the clipped area
        Positioned(
          top: 90.0,
          left: size.width / 2.75,
          child: InkWell(
            splashColor: Colors.red,
            onTap: () {
              print('Yes clicked');
            },
            child:Container(
                height: 90,
                width: 90,
                decoration: const BoxDecoration(
                  image: DecorationImage(
                    image:AssetImage('assets/custom_button.png'),
                ),
              ),
            ),
          ),
        ),
      ],
    ),

```
## Date: 17 June, 2022 (Json Parsing Modifying, Flutter)
add jsonKey inside the field. and inside name parameter define your custom type.
```dart
@JsonKey(name: 'last_name')
  String lastName;
```

## Date: 22 August, 2022 (enum, Flutter)
Declaring a class for mocking data need to instantiate the class with values. Like,

```dart
class Player{
  final String name, position;
  const Player({required this.name, required this.position});
  }
  
  //To use this we have to write like this,
  Player(name:'Messi',position:'RW'),
  Player(name:'Ronaldo',position:'CF');
```

Instead of this, we can instantiate values in the enum.
```dart
enum Player{    
  messi(name:'Messi',position:'RW'),
  ronaldo(name:'Ronaldo',position:'CF');
  
  
  final String name;
  final String position;
  
  const Player({required this.name, required this.position});
}
void main(){
  print(Player.values.map((e)=>(e.name)));
}
```
## Date: 27 September, 2022 (ValueNotifier and ValueListenableBuilder, Flutter)
I was worried about how the ValueNotifier is working without StatefullWidget, this is what I can found!

`fun-fact: this is too much hectic to me, I always prefer flutter_hooks in this condition`

```dart
class MyHomePage extends StatelessWidget {
  MyHomePage({super.key});
  final ValueNotifier<int> _counter = ValueNotifier<int>(0);    //this is instantiation of a int value
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: ValueListenableBuilder(                         // this is the builder that is responsible for changing the UI 
            valueListenable: _counter,
            builder: (context, value, _) {                 
              return Text('Count is $value');               
            }),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          _counter.value++;                                   // this is how we can modify the value
        },
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

## Date: 03 January, 2023 (Pagination, Flutter)
<img src="https://user-images.githubusercontent.com/31488481/210308845-ab73c94f-1b94-4a24-938f-19736262021c.png" width="200" height="400" />

Pagination like this!

`page_properties.dart`

```dart
class PageProps {
  int currentPage;
  int totalPages;
  PageProps({
    required this.currentPage,
    required this.totalPages,
  });

  factory PageProps.initial() => PageProps(currentPage: 0, totalPages: 0);

  @override
  String toString() =>
      'PageProps(currentPage: $currentPage, totalPages: $totalPages)';

  PageProps copyWith({
    int? currentPage,
    int? totalPages,
  }) {
    return PageProps(
      currentPage: currentPage ?? this.currentPage,
      totalPages: totalPages ?? this.totalPages,
    );
  }
}
```

`pagination_bloc.dart`

_For simplicity I use rxDart here to explain the paginating functionality_
```dart
class PaginationBloc{
//declaration
late BehaviorSubject<PageProps> _pageProps;
late BehaviorSubject<List<Datum>> _allData;
late BehaviorSubject<List<Datum>> _dataToDisplay;
int _pageItemLimit = 5;

//getters
Stream<List<Datum>> get getAllData => _dataToDisplay.stream;
bool get canIncreasePage => _pageProps.stream.value.currentPage < _pageProps.stream.value.totalPages;
bool get canDecreasePage => _pageProps.stream.value.currentPage > 0;

void init(){
  _pageProps = BehaviorSubject<PageProps>.seeded(PageProps.initial());
  _allData = BehaviorSubject<List<Datum>>();
  _dataToDisplay = BehaviorSubject<List<Datum>>();
}


//bloc
void getData()async{
  //reset the page props
    _pageProps.sink.add(PageProps.initial());
    final data = await _repo.getData(props);
    if(data.length>0)
    {
      _allData.sink.add(data);
      _updateDataToDisplay();
      
      //add the total page size to _pageProps
        _pageProps.sink.add(
          _pageProps.stream.value.copyWith(
            totalPages: (data.length / _pageItemLimit).ceil() - 1,
          ),
        );
    }
}
 //helper for pagination
  _updateDataToDisplay() {
    int visitedItems = _pageProps.stream.value.currentPage * _pageItemLimit;
    int totalItems = _allData.stream.value.length;
    int currentPage = _pageProps.stream.value.currentPage;

    //check overflow condition for _pageItemLimit
    if (totalItems - visitedItems < 5) {
      _pageItemLimit = totalItems - visitedItems;
    } else {
      _pageItemLimit = 5;
    }

    //checking if overflow condition is false
    if (visitedItems < totalItems) {
      _dataToDisplay.sink.add(
        _allData.stream.value.sublist(
          (currentPage * 5),
          (currentPage * 5) + _pageItemLimit,
        ),
      );
    }
  }
  
   increaseCurrentPage() {
    var currentPage = _pageProps.stream.value.currentPage;
    var totalPages = _pageProps.stream.value.totalPages;
    if (currentPage < totalPages) {
      _pageProps.sink
          .add(_pageProps.stream.value.copyWith(currentPage: currentPage + 1));
      _updateDataToDisplay();
    }
  }

  decreaseCurrentPage() {
    var currentPage = _pageProps.stream.value.currentPage;
    if (currentPage > 0) {
      _pageProps.sink
          .add(_pageProps.stream.value.copyWith(currentPage: currentPage - 1));
      _updateDataToDisplay();
    }
  }
  
  //jump to page
  changeCurrentPage(int page) {
    page = page - 1; //because page(UI) starts from 1, but index starts from 0
    var totalPages = _pageProps.stream.value.totalPages;
    if (page >= 0 && page <= totalPages) {
      _pageProps.sink.add(_pageProps.stream.value.copyWith(currentPage: page));
      searchFieldController.clear();
      _updateDataToDisplay();
    }
  }
  
  void dispose(){
  _pageProps.close();
  _allData.close();
  _dataToDisplay.close();
  }

}
```
Then in the __UI__ we have to listen the __getAllData__ and do the stuffs.


## Date: 14 January, 2023 (Unit Testing, FLutter)
A simple tweak for http unit test, though I don't think it's a good way!
```dart
setUpAll(()=> HttpOverrides.global = null);
```

