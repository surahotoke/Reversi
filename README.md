# リバーシ
HTMLとCSSで作ったリバーシです。

## 構成ファイル
- `index.html`
- `style.css`
- `script.css`

## 遊び方
「置ける」の部分を長押しすると駒を置けます。

## 注意
- なるべく対策はしましたが不安定で、後半はだいぶ変な挙動になるかもしれません。
- 最新のChromeブラウザを推奨しています。Firefox, Safariでは動かなかったです。(Chrome, Edge, Operaでは動作することを確認できました)

## 実際に遊べるページ
https://surahotoke.github.io/Reversi

## プレイ動画
https://github.com/user-attachments/assets/91beee54-63ae-4b89-8c7b-b9d4c8716346

## 仕組み
デカくなっちゃったので見たかったら、拡大してみてね！
```mermaid
sequenceDiagram
    actor 人
    box "入力/UI"
        participant cell_before as .board>div::before
        participant cell_hover_active as .board>div:hover:active
    end
    box "マス本体"
        participant cell as .board>div
    end
    box "位置演算"
        participant WH_after as .WH::after
        participant WH as .WH
    end
    box "方向演算"
        participant LR as .LR
        participant RL as .RL
        participant LR_after as .LR::after
        participant RL_after as .RL::after
    end
    box "ターン"
        participant turn as .turn
    end
    box "伝達"
        participant cell_after as .board>div::after
        participant lr_after as .lr>div::after
        participant rl_after as .rl>div::after
    end
    par
      cell->>cell: sibling-index()で位置数値化
    and
      cell->>cell_hover_active: 同じ要素
    and
      cell->>cell_after: 位置継承
    end
    par
      turn->>cell: ターン継承
    and
      cell->>cell_after: ターン継承
    end
    par
      cell->>cell: 取得判定
    and
      cell->>cell_before: 取得可能マス表示
    end
    人->>cell_before: クリック(長押し)
    Activate 人
    par
      cell_before->>cell: こっちに吸われる
    and
      cell->>cell_hover_active: 発動
    end
    cell_hover_active->>cell_hover_active: 押下位置エンコード
    cell_hover_active->>WH_after: counter(WH)
    WH_after->>WH_after: contentに設定
    WH_after->>WH: サイズ引き継ぎ
    WH->>WH: サイズ数値化
    WH->>WH: 押下位置デコード
    par
      WH->>LR: 押下位置継承
    and
      LR->>RL: 押下位置継承
    and
      RL->>turn: 押下位置継承
    and
      turn->>cell: 押下位置継承
    and
      cell->>cell_after: 押下位置継承
    end
    cell_after->lr_after: 押下なら、4方向取得数contentに設定
    cell_after->rl_after: 押下なら、4方向取得数contentに設定
    cell_after->>cell_after: デコード
    cell_after->>cell_after: エンコード
    cell_after->>RL_after: counter(4方向取得数)
    cell_after->>LR_after: counter(4方向取得数)
    LR_after->>LR: サイズ引き継ぎ
    RL_after->>RL: サイズ引き継ぎ
    LR->>LR: サイズ数値化
    RL->>RL: サイズ数値化
    LR->>LR: 4方向取得数デコード
    RL->>RL: 4方向取得数デコード
    LR->>RL: 4方向取得数継承
    RL->>RL: 8方向取得マス計算
    RL->>cell: 8方向取得マス継承
    par
      Note over turn: 押下なら、ターン切り替えアニメーション
      Activate turn
    and
      Note over cell: 押下位置なら、配置アニメーション
      Activate cell
    and
      Note over cell: 反転可能位置なら、反転アニメーション
    end
    par
      Note over cell: 反転アニメーション終了
    and
      Note right of cell: 取得可能表示が消滅
    and
      cell->>cell: --guideによりこれ自体に判定付与
    and
      人->>cell: こっちに移る(同じ要素なので:hover:activeは発動)
    and
      Note over cell: アニメーション継続
    end
    par
      Note over cell: 配置アニメーション終了
      Deactivate cell
    and
      Note right of turn: --guideが切れて判定が消滅
    and
      turn->>turn: --guideによりこれに判定付与
    and
      人->>turn: こっちに移る(親要素なので:hover:activeは発動)
    and
      Note over turn: アニメーション継続
    end
    Note over turn: ターン切り替えアニメーション終了
    Deactivate turn
    Note right of turn: --guideが切れて判定が消滅
    Note over 人: 長押し終了
    Deactivate 人
```