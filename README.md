# firebase-get-to-know-flutter

firestore에 저장한 데이터를 변수에 저장하는 방법을 알고싶었다.
내게 필요한 코드:
main.dart의 ApplicationState

  Future<void> init() async {
    await Firebase.initializeApp();

    FirebaseAuth.instance.userChanges().listen((user) {
      if (user != null) {
        _loginState = ApplicationLoginState.loggedIn;
        //가장 궁금했던 부분. 저장된 데이터를 가져와서 List<객체>에 저장해놓는다.
        _guestBookSubscription = FirebaseFirestore.instance
            .collection('guestbook')
            .orderBy('timestamp', descending: true)
            .snapshots()
            .listen((snapshot) {
          _guestBookMessages = [];
          snapshot.docs.forEach((document) {
            _guestBookMessages.add(
              GuestBookMessage(
                name: (document.data()['name']).toString(),
                message: (document.data()['text']).toString(),
              ),
            );
          });
          notifyListeners();
        });
      } else {
        _loginState = ApplicationLoginState.loggedOut;
        _guestBookMessages = [];
        _guestBookSubscription?.cancel();
      }
      notifyListeners();
    });
  }