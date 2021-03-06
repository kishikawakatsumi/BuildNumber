BuildNumber
===========

### 準備１

1. Build Settings > `Preprocess Info.plist` => `Yes`
2. Build Settings > `Info.plist Preprocessor Prefix File` => `BuildNumber.h`
3. Build Settings > `Info.plist Preprosessor Definitions` => `DEBUG=1` (必要なら)

### 準備２

準備１が済んだら、続けて下記の設定を追加する
Build PhaseにRun Scriptをビルドの前後に下記のように設定する。

- ビルド前
  `touch BuildNumber/BuildNumber-Info.plist`

- ビルド後

   `sh build_number.sh`

### 準備３

最後に、Info.plistの`Bundle versions string, short`のところには`MARKETING_VERSION`、
`Bundle version`には`BUILD_NUMBER`と入力する。


### 解説

準備１でInfo.plistのプリプロセスが有効になる。  
準備３で設定した`MARKETING_VERSION`と`BUILD_NUMBER`はプリプロセスの過程で置換される。  
置換する文字列は`Preprocessor Prefix File`として指定した`BuildNumber.h`にマクロとして記入しておく。  
`BuildNumber.h`をRun Scriptフェーズで生成する。  

このようにすることで、Info.plistを変更すること無く、置換元のBuildNumber.hを書き換えることで、バージョンを書き換えることが可能になる。  
`BuildNumber.h`は`.gitignore`で無視しておけるので、`BuildNumber.h`を書き換えても差分は出ない。  

ビルド前に`touch BuildNumber/BuildNumber-Info.plist`としているのは、Info.plistの更新日付を新しくしないと、
差分ビルドでInfo.plistが変更対象にならないので、クリーンビルドしないとバージョンの変更が反映されないため。

### 問題点
初回だけは`BuildNumber.h`のファイルを作っておかないと(`touch BuildNumber.h`)、ビルドできない。  
Compile Soucesの前にRun Scriptで作るのでは遅くてダメだった。  
