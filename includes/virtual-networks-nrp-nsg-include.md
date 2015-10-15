## ネットワーク セキュリティ グループ (NSG)
NSG リソースを使用すると、許可または拒否の規則を実装することで、ワークロードのセキュリティ境界を作成できます。このような規則は、NIC レベル (VM インスタンス レベル) またはサブネット レベル (VM のグループ) で適用されます。

NSG リソースの主なプロパティは次のとおりです。

- **セキュリティ規則** – NSG には複数のセキュリティ規則を定義できます。各規則で、各種のトラフィックを許可または拒否できます。

### セキュリティ規則
セキュリティ規則は、NSG の子リソースです。

セキュリティの規則の主なプロパティは次のとおりです。

- **プロトコル** – この規則が適用されるネットワーク プロトコル。
- **送信元ポート範囲** – 送信元ポート、または 0 ～ 65535 の範囲。すべてのポートを適合させるために、ワイルドカードを使用できます。 
- **送信先ポート範囲** – 送信先ポート、または 0 ～ 65535 の範囲。すべてのポートを適合させるために、ワイルドカードを使用できます。
- **送信元アドレスのプレフィックス** – 送信元 IP アドレスの範囲。 
- **送信先アドレスのプレフィックス** – 送信先 IP アドレスの範囲。
- **アクセス** – トラフィックの*許可*または*拒否*。
- **優先度** – 100 から 4096 までの値。セキュリティ規則のコレクションでは、優先度番号は各規則に対して一意である必要があります。優先度番号が低いほど、規則の優先度が高くなります。
- **方向** – 規則をトラフィックに適用する方向を、*受信*方向にするか、それとも*送信*方向にするかを指定します。 

<!---HONumber=Sept15_HO4-->