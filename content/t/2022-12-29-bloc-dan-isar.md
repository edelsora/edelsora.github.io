---
title: "Catatan Flutter: Bloc dan Isar"
description: Sebuah state management dan embedded database di flutter
---

## Bloc
- State managemen model-update, mirip mvu tanpa V di flutter. 
- Bloc punya state (Model di MVU) tempat nyimpen data sementara dan event (Update di MVU) tempat ngubah data, kalo di react mirip redux, kalo di otomata mirip *Non-Deterministic Finite Automata* punya state, rule dan data.
- State management hanya tempat menyimpan data dari aksi/event/update, jadi sangat wajar untuk state berganti jenis kelasnya, state Initial, state Wait dan state Result.
- Bloc berkomunikasi antar widget dengan passing lewat build context, kalo di JS familiy ini  mirip pasing prop dan implicit tapi flutter totaly explicit.
- Presentasi Bloc dan ListView bersifat orthogonal, jadi perubahan data harus handle di kedua pihak, baik di index list view dan send event untuk aksi data, ini juga berlaku untuk presetation layer/widget yang punya state sendiri.
```dart
onPressed: (){
	results = results.removeAt(indexItem); // handle listview
	commandBloc.add(CommandDelete(item.id)); // handle bloc
}
```
- Kalau ada modifikasi ada yang pindah ke halaman lain (navigate), trigger bloc untuk rerender listview setelah balik ke halaman asal via callback *whenComplete*, pakai then malah ada miss dan ga trigger event, sebab then ga bersifat finally yang mana jadi end hook buat semua kondisi.
```dart
Navigator.of(context)
    .push(...)
    .whenComplete(()=> queryBloc.add(QueryPagging())) 
// ini ketika tutup halaman push dari router utama,
// bakal trigger bloc buat rerender list view
```
-  Halaman navigate rekomended pakai BlocConsumer untuk listen perubahan Blocnya, supaya ketika trigger event bisa kelacak perubahan apalagi yang ada navigator pop (balik ke halaman asli).
```dart
BlocConsumer<BlocCommand, BlocCommandState>(
    bloc: command,
    listener: (listenerCtx, listenerState) {
      if (state is CommandResponse) {
        // ini untuk balik ke halaman utama 
        // setelah selesai task blocnya
        Navigator.pop(context, true);
      }
    },

    builder: (builderCtx, builderState) {
	    // UI 
	    onPressed: () {
		    commandBloc.add(CommandAdd(...))
	    }
    }
  )
```

- Kalau ada modifikasi enggak pindah halaman (stay on page), tangkep perubahan state dari bloc untuk rerender UI.

```dart
// anggep dalam fungsi build widget
if(state is StateInitial){
    return // saat initial
}

if(state is StateWait){
    return // saat menunggu hasil bloc
}


if(state is StateResult){
    return // saat mendapat hasil dari bloc
}
```

- Kalau ada perubahan UI yang sifatnya stay on page, pastiin nge-catch kondisi di presentation layer (widget) supaya ke rerender, dan jangan lupa initial state selalu netral ga ada uinya.

- Bloc Provider mirip Provider di Javascript tapi lebih eksplitsit untuk aksesnya terutama pas push navigation yang bikin BuildContext baru, harus inject build context berisi provider bloc kesana.
```dart
// ini di define di widget paling awal kek MaterialApp dkk
BlocProivder(
	create: (_) => BlocQuery()
	child: // Widget apapun yang punya build context dan bloc builder
)

// Anggep aja kode dibawah ini sudah masuk widget

// Akses Bloc dari BlocProvider lewat BuildContext
BlocQuery query = BlocProvide.of<BlocQuery>(context) // contet didapet dari Widget 

BlocBuilder(
   bloc: query // pakaai bloc yang didapet dari BlocProvider
   builder: (queryCtx,queryState){ // penting penamaan harus dibedakan biar tidak rancu
   }
)
```
- Bloc dengan EventMapperState yang berisi fungsi asynchronous harus punya state Wait untuk ngindikasiin dia lagi kerja, ini sama aja mirip await tapi level Bloc layer, jadi saat trigger fungsi ini bakal emit state Wait dan emit Result state untuk rerender UI.
	- state Wait, munculin UI loading
	- state Result, munculin hasil UI
```dart
abstract class BlocQueryState{}

class BlocQueryInitial extends BlocQueryState {}

class BlocQueryWait extends BlocQueryState {}

class BlocQueryResult extends BlocQueryState {
	final List<String> results; // ganti dengan tipe data sesuai kebutuhan
	const BlockQueryResult(this.results);
}

// didalem widget, nyambung sama yang atas
Scaffold(
	child: BlocProvider(
		bloc: query,
		builder: (queryCtx,queryState){
			// tangkep perubahan state
			if (queryState is BlocQueryWait){
				// return circlerindicator
			}else{
				var results = queryState.results;
				// sajikan isi results  dalam listview / etc
			}
		}
	)
)
```
- Memahami MVU membantu berkenalan dengan mekanism Bloc, tetapi tidak untuk implementasi ala flutter way yang explisit / OOP-ish.
- Saya masih bodoh masalah Equatable, yang mungkin bisa menghapus keperluan akan state Wait dan langsung rerender dari state Result, ini hanya hipotesis.
- Bloc bagus, dokumentasinya bagus, contoh kode dari repositori resminya berantakan dan kurang noob-friendly. Todo List DDD, wtf, walaupun saya juga ketularan kacaunya bikin CQRS dengan Bloc. Dokumentasinya bagus tapi saya pribadi kesulitan menerima maksudnya, ga jauh beda pusingnya baca dokumentasi Redux atau RxJs.

## Isar
- Non-SQL ACID-compliant Full-Text-Search database untuk flutter, elasticsearchnya flutter *jokes aside*.
- Setiap akses haru berupa transaksi.
```dart
await isar.writeTxn(async(){}) // transaksi untuk write ke db
await isar.txt(async(){}) // transaksi untuk query data dari db
```
- Jika pakai Bloc dengan fungsi Asynchronous, maka cocok dengan Isar karena semuanya Asynchornous.
```dart
on<CommandDelete>((event, emit) async {
      emit(CommandWait()); // untuk indikator loading dan rerender ui
      await db.writeTxn(() async {
        await db.dataUnits.delete(event.id);
        emit(CommandResult(null)); // respon hasil dan rereneder ui
      });
    });

on<CommandAdd>((event, emit) async {
      emit(CommandWait()); 
      await db.writeTxn(() async {
        final id = await db.dataUnits.put(event.item);
        emit(CommandResult(id)); 
      });
    });

on<CommandUpdate>((event, emit) async {
      emit(CommandWait());
      await db.writeTxn(() async {
        final id = await db.dataUnits.put(event.updatedItem);
        emit(CommandResult(id));
      });
    });
```
- Punya performa yang lumayan cepat, hampir mirip pakai array biasa karena loadingnya tidak terlalu lama.
- Method untuk handlenya lumayan powerfull, ada aroma-aroma LINQ didalamnya.
- Bergantung dengan .
- Membuat table atau struktur data didatabase menggunakan `@collection` dan `builder_runner`

```dart
// import isar
part of 'item.g.dart';

class Item{
    Id? id;
    String? title;

    Item(this.id): id = Isar.autoIncrement;
}

// setelah itu jalankan : flutter pub run build_runner build
```
- Memuat Isar saya membuat satu instance dan menurunkanya ke BlocProvider (pastikan suddah run builder_runner):
```dart
void main(){
    Isar.open(
        [ItemSchema]
    ).then((db) => {
        runApp(App(db))
    })
}

// Widget dengan parameter yang perlu db,
// hanya konsep bukan working code.
class App extends StatelessWidget{
    final Isar db;
    App(this.db);

    // fungsi build bla bal

    MultiBlocProvider(
        providers:[
            BlocProvider(
                create: (_) => BlocQuery(db);
            ),
            BlocProvider(
                create: (_) => BlocCommand(db);
            )
        ],
        child: // ini home page/etc
    )
}
```

Sekian  dulu, kalau saya ketemu yang baru lagi nanti ditambah.