---
slug: /ja/guides/sre/scaling-clusters
sidebar_label: シャード再調整
sidebar_position: 20
description: ClickHouseは自動シャード再調整をサポートしていないため、シャードを再調整するためのベストプラクティスを提供します。
---

# データの再調整

ClickHouseは自動シャード再調整をサポートしていませんが、以下の優先順位でシャードを再調整する方法があります:

1. [分散テーブル](/docs/ja/engines/table-engines/special/distributed.md)のシャードを調整し、新しいシャードに書き込みを偏らせることができます。これにより、クラスタ内で負荷の不均衡やホットスポットが発生する可能性がありますが、書き込みスループットが非常に高くないほとんどのシナリオでは実行可能です。ユーザーは書き込みのターゲットを変える必要がなく、分散テーブルのままで済みます。ただし、既存のデータの再調整には役立ちません。

2. (1) の代替として、既存のクラスタを修正し、新しいシャードにのみ書き込むことでクラスタがバランスされるようにします。これも書き込みの重み付けを手動で行うことになります。(1)と同じ制限があります。

3. 既存のデータの再調整が必要で、データをパーティション化している場合は、パーティションを切り離して別のノードに手動で移動し、新しいシャードに再アタッチすることを検討してください。これは後の技術より手動が多くなりますが、より迅速でリソースを消費しない場合があります。これは手動操作であり、データの再調整を考慮する必要があります。

4. ソースクラスタから新しいクラスタにデータをエクスポートするために、[INSERT FROM SELECT](/docs/ja/sql-reference/statements/insert-into.md/#inserting-the-results-of-select)を使用します。これにより、非常に大きなデータセットではパフォーマンスが低下し、ソースクラスタに大幅なIOを発生させ、ネットワークリソースをかなり消費します。これは最終手段です。
