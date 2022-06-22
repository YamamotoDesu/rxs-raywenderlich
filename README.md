# RxSwift: Reactive Programming with Swift: Materials

This repo contains all the downloadable materials and projects associated with the **[RxSwift: Reactive Programming with Swift](https://store.raywenderlich.com/products/rxswift)** from [raywenderlich.com](https://www.raywenderlich.com).

Each edition has its own branch, named `editions/[EDITION]`. The default branch for this repo is for the most recent edition.

## Release History

| Branch                                                                           | Edition | Release Date |
| -------------------------------------------------------------------------------- |:-------:|:------------:|
| [editions/4.0](https://github.com/raywenderlich/rxs-materials/tree/editions/4.0) | 4.0     | 2020-08-11   |


<img width="566" alt="スクリーンショット 2022-06-21 22 02 38" src="https://user-images.githubusercontent.com/47273077/174805700-84041c0d-ccd2-423b-bdd5-cbd69e047a8a.png">

<img width="1387" alt="スクリーンショット_2022_06_21_22_03" src="https://user-images.githubusercontent.com/47273077/174805801-4ff35fa1-0078-4750-bdb7-66183cdce1ef.png">

## What would happen if you did not add a completed or error event, and also didn’t add the subscription to disposeBag? 
```swift
example(of: "create") {
  enum MyError: Error {
    case anError
  }

  let disposeBag = DisposeBag()

  Observable<String>.create { observer in
    // 1
    observer.onNext("1")

//    observer.onError(MyError.anError)

    // 2
//    observer.onCompleted()

    // 3
    observer.onNext("?")

    // 4
    return Disposables.create()
  }
  .subscribe(
    onNext: { print($0) },
    onError: { print($0) },
    onCompleted: { print("Completed") },
    onDisposed: { print("Disposed") }
  )
//  .disposed(by: disposeBag)
}

```

<img width="425" alt="スクリーンショット_2022_06_22_21_52" src="https://user-images.githubusercontent.com/47273077/175033720-8efa31ab-c510-4a05-8944-7ab048a0dd72.png">


## Using Traits
```swift
example(of: "Single") {
  // 1
  let disposeBag = DisposeBag()

  // 2
  enum FileReadError: Error {
    case fileNotFound, unreadable, encodingFailed
  }

  // 3
  func loadText(from name: String) -> Single<String> {
    // 4
    return Single.create { single in
        // 1
        let disposable = Disposables.create()

        // 2
        guard let path = Bundle.main.path(forResource: name, ofType: "txt") else {
          single(.error(FileReadError.fileNotFound))
          return disposable
        }

        // 3
        guard let data = FileManager.default.contents(atPath: path) else {
          single(.error(FileReadError.unreadable))
          return disposable
        }

        // 4
        guard let contents = String(data: data, encoding: .utf8) else {
          single(.error(FileReadError.encodingFailed))
          return disposable
        }

        // 5
        single(.success(contents))
        return disposable
    }
  }
    
    // 1
    loadText(from: "Copyright")
    // 2
        .subscribe {
            // 3
            switch $0 {
            case .success(let string):
                print(string)
            case .error(let error):
                print(error)
            }
        }
        .disposed(by: disposeBag)

}

```


