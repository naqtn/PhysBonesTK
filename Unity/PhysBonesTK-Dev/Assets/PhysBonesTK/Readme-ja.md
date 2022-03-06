# PhysBonesTK readme

PhysBonesTK は VRChat Avatar Dynamics の PhysBones を調整するキット（Tuning Kit）です。
PhysBone のパラメータを実行時に（つまり VRChat の中で）変更できます。

## デモ / 基本的な使い方


デモ：

- [デモアバターを配置したワールド](https://vrchat.com/home/launch?worldId=wrld_1935fd5a-ff63-4cfa-92c9-21f22f850e74) を用意しました。
- PhysBone で動かされるボーンが、デモアバターの右腕に付いています。
- PhysBone のパラメタを Expression Menu から変更できます。
- デスクトップモードの場合は、ワールド内の何かをピックアップしてください。


メニュー一覧：

- "Example Configs": パラメタの設定例を選んでロードします
- "Movement parameters": PhysBone の物理的なパラメータを変更します (Pull, Spring, など) 
- "Interaction parameters": PhysBone のインタラクション関連のパラメタを変更します (CollisionRadius, AllowGrabbing, など)
- "Move/Scale": アイテムをワールド設置するなど （"accessory item" 適用ケースの場合にのみ）


パラメタの値：

この節では、メニューに表示される百分率の値と PhysBone の実際のパラメタの値の関係を説明します。
これにより PhysBonesTK で調整した後に、PhysBone を直接に設定できます。

基本の関係 0% => 0, 50% => 0.5, 100% => 1.0

以下は、例外的な関係になっているものです。

- "Gravity":
  - "Narrow Range" off: 0% => 0, 100% => 1.0 
  - "Narrow Range" on:  0% => 0, 100% => 0.1 
  - "Negative" on: 上記の値を負にしたもの
- "Max Angle":
  - Narrow off: 0% => 0, 100% => 180
  - Narrow on: 0% => 0, 100% => 10
- "FreezeAxis Angle": 0% => 0, 100% => 180
- "CollisionRadius": 0% => 0, 100% => 0.1 (meaning 10cm if Transform Scale is 1.0)
- "MaxStretch":  0% => 0, 100% => 5.0


## PhysBonesTK をアバターにインストールする方法

PhysBoneTK には二種類の適用方法（ユースケース）があります。

1. アバターの本体のボーンに使用する（PhysBone がアバターのボーンの一部を駆動する。"avatar body bone" と表記）
2. アバターのアクセサリアイテムに使用する（PhysBone が独立したアイテムのボーンを駆動する。"avatar accessory item" と表記）

後者は追加の機能を持っています。たとえば

- 表示 on/off 切り替え（Show or hide）
- アイテムのワールド固定
- アイテムの大きさ変更

（これらはテスト環境を作るため、あるいは、楽しみのために作りました。PhysBone の技術的な事情ではなくて。）


### "Avatar Body Bone" の場合の基本的な導入方法

1. プレハブをアバターに入れる
  1. プロジェクト中の `Assets/PhysBonesTK/ForAvatarBodyBone` フォルダをアセット選択のために開く
  2. `PhysBonesTK` プレハブをアバターのルートの GameObject に追加する
2. VRCPhysBone の root bone を設定する
  1. ヒエラルキー・ウィンドウで、ステップ 1 で追加したプレハブの `PhysBonesTK/PhysBone` GameObject を選択する
  2. インスペクタで `VRCPhysBone` コンポーネントの `RootTransform` フィールドに PhysBone を適用したいアバターのボーンを設定する
3. avatar descriptor を設定する
  1. インスペクタでアバターの `VRCAvatarDescriptor` を開く
  2. 以下のアセットを拾うためにプロジェクト・ウィンドウで `Assets/PhysBonesTK/ForAvatarBodyBone` フォルダを開く
  3. `PBTK_Controller` アセットを `VRCAvatarDescriptor/PlayableLayers/Base/FX` にセットする
  4. `PBTK_Menu` アセットを `VRCAvatarDescriptor/Expressions/Menu` にセットする
  5. `PBTK_Parameters` アセットを `VRCAvatarDescriptor/Expressions/Parameters` にセットする

作業後の構造は以下のようになります：

    avatar (VRCAvatarDescriptor holds FX Controller, Menu, Parameters assets of PhysBonesTK for this usage)
     - avatar bones (or armature)
     - avatar mesh
     - PhysBonesTK
       - PhysBone (PhysBone RootTransform points somewhere of your avatar bone)


### "Avatar Accessory Item" の場合の基本的な導入方法

この利用方法では、アバターのボーン構造とは分離したボーン構造が必要です。

この利用方法では "world constraint", "move position", "changing scale" のような機能を提供します。
これらは PhysBone のパラメタではないので、これらの機能が不要であれば
分離したボーン構造を使うのであっても "Avatar Body Bone" の方法を使えます。
（"Avatar Body Bone" は expression parameters の使用量が少ないので、
状況によってはそちらを使った方が良いかもしれません。）


1. プレハブをアバターに入れる
  1. プロジェクト中の `Assets/PhysBonesTK/ForAvatarAccessoryItem` フォルダをアセット選択のために開く
  2. `PhysBonesTK` プレハブをアバターのルートの GameObject に追加する
2. アクセサリ・アイテムをプレハブの中に置く
  1. ヒエラルキー・ウィンドウで、ステップ 1 で追加したプレハブの `PhysBonesTK/WorldOrigin/ItemContainer/Item` GameObject を選択する
  3. 使いたいアクセサリ・アイテムの GameObject を `Item` の子として追加する.
3. VRCPhysBone の root bone を設定する
  1. インスペクタでプレハブの `PhysBonesTK/PhysBone` GameObject を開く
  2. インスペクタで `VRCPhysBone` コンポーネントの `RootTransform` フィールドに PhysBone を適用したいアイテムのボーンを設定する
4. ワールド固定のために constraint コンポーネントを設定する
  1. アバターのボーン構造の中の（ワールド固定していない時に）アイテムを配置したい場所に、新しい GameObject を追加する。以下この GameObject を `attachPoint` と呼びます。
  2. `attachPoint` の position と rotation を調整する
  3. プレハブの `PhysBonesTK/WorldOrigin/ItemContainer` GameObject をインスペクタで開く
  4. `attachPoint` を `ParentConstraint/Sources` に設定する（その欄は初期状態では `None` になっている）
  5. `ParentConstraint` コンポーネントの "Zero" ボタンを押す。（アイテムは `attachPoint` へ移動する）
  6. （位置が適当でなければ `attachPoint` の  position と rotation を再び調整する）
3. avatar descriptor を設定する
  1. インスペクタでアバターの `VRCAvatarDescriptor` を開く
  2. 以下のアセットを拾うためにプロジェクト・ウィンドウで `Assets/PhysBonesTK/ForAvatarAccessoryItem` フォルダを開く
  3. `PBTK_Controller` アセットを `VRCAvatarDescriptor/PlayableLayers/Base/FX` にセットする
  4. `PBTK_Menu` アセットを `VRCAvatarDescriptor/Expressions/Menu` にセットする
  5. `PBTK_Parameters` アセットを `VRCAvatarDescriptor/Expressions/Parameters` にセットする

作業後の構造は以下のようになります：

    avatar (VRCAvatarDescriptor holds FX Controller, Menu, Parameters assets of PhysBonesTK for this usage)
     - avatar bones (or armature)
       - some bone where item attached
         - attachPoint (attach target transform. parent constraint source)
     - avatar mesh
     - PhysBonesTK
       - PhysBone  (PhysBone RootTransform points somewhere of your item bone)
       - WorldOrigin
         - ItemContainer (ParentConstraint Sources points attachPoint)
           - Item (your accessory item)
             - item bones
             - item mesh (optional.)

注意：
- GameObject の名前はアニメーションがそれを使っているので重要です
- このため同じ名前と構造を使う必要があります。（あるいは対応するアニメーション・クリップを編集します）
- 上記の構造図では、GameObject 名前の先頭が大文字になっているのはこの命名規則を示しています


## 制限事項

- 幾つかの機能は late joiner に同期しません。（失われる PBTK_Command パラメタを使っている部分が該当します）

## 技術資料

英語版 (Readme)[Readme.md] を参照してください。

