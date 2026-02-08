# Zaim → Beanpost 勘定科目設計

## 2軸の分類体系

- **軸1: Zaim Category** = 支出の質的分類（消費の目的・文脈）
  - Food, Pleasure, Casual, Close, Work, etc.
- **軸2: 費目別分類** = Beanpost 勘定科目（支出の対象の性質による分類）
  - 以下の4大区分に基づく

---

## 費目別分類（軸2）— 費用の4大区分

費用とは何に対して貨幣を支払ったかで分類される。以下の基準により4区分に大別される。

1. **裁量性**: 支出するかどうかを自分で決められるか
2. **対象の性質**: 有形物か無形か
3. **主体性**: 自分が主体的に行使するか、相手に何かをしてもらうか

| 区分 | 裁量性 | 対象 | 主体 |
|---|---|---|---|
| Goods | 裁量的 | 有形物 | — (所有権の移転) |
| Services | 裁量的 | 無形 | 相手が行為する |
| Rights | 裁量的 | 無形 | 自分が行使する |
| Levies | 非裁量的 | — | — (法的義務) |

> - 債務の返済は B/S 内の取引（負債の減少と資産の減少）であり、費用ではない
> - 贈与は、贈与した対象の性質（財・役務・権利）により上記のいずれかに分類される

### 1. 財の取得 (Goods)

有形物の所有権を貨幣と交換して取得する取引。

| 細分類 | 説明 | 例 |
|---|---|---|
| 即時消費財 | 取得後すぐに消費される財 | 食材、ガソリン、飲料 |
| 短期消耗財 | 数日〜数ヶ月で消費される財 | 日用品、化粧品、洗剤、文具 |
| 耐久財 | 年単位で便益を提供する財（減価償却の対象となりうる） | 家電、家具、衣類、書籍、ガジェット |
| 医薬品 | 健康の回復・維持のための財 | 薬 |

### 2. 役務の対価 (Services)

相手に何かをしてもらい、その対価として貨幣を支払う取引。相手が主体となって行為する。

| 細分類 | 説明 | 例 |
|---|---|---|
| 運輸 | 移動サービスの対価 | 電車、バス、タクシー、高速道路通行料 |
| 飲食 | 調理・提供サービスの対価（食材原価を含む） | 外食、カフェ |
| 医療 | 診療・治療サービスの対価 | 診察、手術、歯科矯正 |
| 身体ケア | 美容・リラクゼーションサービスの対価 | 美容院、マッサージ、スパ、エステ |
| クリーニング | 洗浄サービスの対価 | クリーニング |
| 廃棄 | 廃棄物処理サービスの対価 | ゴミ処理 |
| 物流 | 配送サービスの対価 | 送料、宅配便 |
| 教育 | 教育・指導サービスの対価 | 授業料、受験料 |
| 金融 | 金融サービスの対価 | 手数料、利息 |
| 駐車 | 駐車場の一時利用サービスの対価 | 時間貸し駐車場 |

### 3. 権利の取得 (Rights)

自分が主体的に行使できる権利を貨幣と交換して取得する取引。利用の有無にかかわらず支払義務が発生する。

| 細分類 | 説明 | 例 |
|---|---|---|
| 賃借権 | 不動産・動産の使用権 | 家賃、月極駐車場 |
| 利用権 | ソフトウェア・サービスの利用権 | サブスクリプション (LLM, Notion, Kindle Unlimited, Zaim, Amazon Prime 等) |
| 保障権 | 保障を受ける権利 | 生命保険、火災保険、自動車保険 |
| 通信権 | 通信サービスの利用権 | 携帯電話回線、インターネット回線 |

### 4. 公課 (Levies)

法的義務として課される非裁量的な支払。

| 細分類 | 説明 | 例 |
|---|---|---|
| 所得課税 | 所得に対する課税 | 所得税、住民税 |
| 社会保険 | 社会保障制度への強制拠出 | 健康保険、厚生年金、雇用保険 |

---

## 機能別分類 — 費目別分類からの配賦先

費目別分類（Goods/Services/Rights/Levies）で一次集計した費用を、目的・機能別に配賦して二次集計するための分類。
Zaim Category（軸1）の上位集約に相当する。

| 機能別 | 説明 | Zaim Category（軸1） |
|---|---|---|
| 生活 (Living) | 日常生活の基本的な維持 | Food, Infra |
| 間接費 (Overhead) | 生活を支える間接的な支出 | Overhead, Maintenance |
| 娯楽 (Leisure) | 個人の楽しみのための支出 | Pleasure |
| 交際 (Social) | 人間関係の構築・維持のための支出 | Casual, Close, Work |
| 健康 (Health) | 健康の維持・回復のための支出 | Health |
| 教育 (Education) | 知識・技能の獲得のための支出 | Education, Learning |
| 社会義務 (Obligation) | 税・社会保険等の義務的支出 | Obligation |

> 費目別→機能別の配賦例:
> - Goods:即時消費財(ガソリン) → 配賦先は取引の文脈により決定（生活/交際/娯楽...）
> - Services:飲食(外食) → Casual なら交際、Pleasure なら娯楽
> - Rights:利用権(Notion) → 間接費
> - Levies:所得課税 → 社会義務

---

## 現状: Zaim Category × Genre（支出 payment）

### Food (1,079件)
| Genre | 件数 |
|---|---|
| Groceries | 1,041 |
| Stable | 38 |

### Pleasure (365件)
| Genre | 件数 |
|---|---|
| Snacks | 190 |
| Eatery | 91 |
| Cafe | 54 |
| Spa | 11 |
| Other | 9 |
| Books | 5 |
| Massage | 2 |
| Cartoon | 2 |
| Leisure | 1 |

### Items (182件)
| Genre | 件数 |
|---|---|
| Hygiene | 50 |
| Cosme | 34 |
| Kitchen | 26 |
| Other | 22 |
| Clothes | 22 |
| Gadgets | 13 |
| Toilet | 11 |
| Shoes | 3 |
| Dining | 1 |

### Casual (156件)
| Genre | 件数 |
|---|---|
| Friends | 103 |
| Temporary | 53 |

### Transport (147件)
| Genre | 件数 |
|---|---|
| Gasoline | 91 |
| Train | 42 |
| Tolls | 8 |
| Parking | 6 |

### Maintenance (69件)
| Genre | 件数 |
|---|---|
| Laundry | 58 |
| Gabage | 6 |
| Car | 5 |

### Obligation (46件)
| Genre | 件数 |
|---|---|
| Pension | 11 |
| Employment Insurance | 11 |
| Health Insurance | 11 |
| Income tax | 11 |
| Residence tax | 10 |
| Other | 2 |
| Vehicle tax | 1 |

### Close (45件)
| Genre | 件数 |
|---|---|
| Father | 16 |
| Mother | 9 |
| Sister | 8 |
| Partner | 7 |
| Relatives | 3 |
| Other | 1 |
| Brother | 1 |

### Infra (36件)
| Genre | 件数 |
|---|---|
| Rent | 13 |
| Phone | 9 |
| Water | 5 |
| Electricity | 4 |
| Gas | 3 |
| Car Insurance | 1 |
| Fire Insurance | 1 |

### Work (30件)
| Genre | 件数 |
|---|---|
| Business Party | 11 |
| Coworkers | 10 |
| Clothes | 7 |
| Shoes | 2 |

### Overhead (27件)
| Genre | 件数 |
|---|---|
| Console | 11 |
| logistics | 6 |
| LLM | 5 |
| Other | 4 |
| Finance | 1 |

### Health (22件)
| Genre | 件数 |
|---|---|
| Hospital | 9 |
| Fee | 5 |
| Beauty Salon | 3 |
| Medicine | 2 |
| Orthodontina | 2 |
| Initial | 1 |

### Education (15件)
| Genre | 件数 |
|---|---|
| Tuition | 10 |
| Examination | 3 |
| Books | 1 |
| Other | 1 |

### Learning (7件)
| Genre | 件数 |
|---|---|
| Books | 7 |

### Other (5件)
| Genre | 件数 |
|---|---|
| Other | 4 |
| Allowance | 1 |

---

## 現状: Zaim Category（収入 income）

| Category | 件数 |
|---|---|
| Salary | 17 |
| Other | 22 |
| Commuting Allowance | 6 |
| Business income | 2 |
| Bonus | 1 |
| Extraordinary revenue | 1 |

---

## 現状: Zaim Account（口座）

### Assets
| Zaim Account | Prefix | 種別 |
|---|---|---|
| BK_82 | BK_ | 銀行 |
| BK_JP_POST | BK_ | 銀行 |
| BK_JRE | BK_ | 銀行 |
| BK_MUFG | BK_ | 銀行 |
| BK_RAKUTEN | BK_ | 銀行 |
| BK_SMBC | BK_ | 銀行 |
| CY_MOBILE_SUICA | CY_ | 電子マネー |
| CY_PASUMO | CY_ | 電子マネー |
| CY_PAYPAY | CY_ | 電子マネー |
| CY_WALLET | CY_ | 現金 |
| DP_FACILITY | DP_ | デポジット |
| DP_PRIVATE | DP_ | デポジット |
| DP_STARBUCKS | DP_ | デポジット |
| DP_TATEISHI | DP_ | デポジット |

### Liabilities
| Zaim Account | Prefix | 種別 |
|---|---|---|
| CC_AMAZON | CC_ | クレジットカード |
| CC_MER_CARD | CC_ | クレジットカード |
| CC_RAKUTEN_JCB | CC_ | クレジットカード |
| CC_RAKUTEN_MASTERCARD | CC_ | クレジットカード |
| CC_VIEW | CC_ | クレジットカード |
| CC_YODOBASHI | CC_ | クレジットカード |
| CD_PRIVATE | CD_ | クレジットデポジット |
| LN_ORTHODONTINA | LN_ | ローン |
| LN_STUDENT | LN_ | ローン |
| LN_TUITION_CPA | LN_ | ローン |
| AD_Electricity | AD_ | 自動引落 |
| AD_Gas | AD_ | 自動引落 |
| AD_Monthly | AD_ | 自動引落 |
| AD_Phone | AD_ | 自動引落 |
| AD_Water | AD_ | 自動引落 |
| PD_Amazon_Prime | PD_ | 定期引落 |
| PD_CPA | PD_ | 定期引落 |
| PD_Car_Insurance | PD_ | 定期引落 |
| PD_Claude | PD_ | 定期引落 |
| PD_Fire_Insurance | PD_ | 定期引落 |
| PD_Gym | PD_ | 定期引落 |
| PD_Kindle_Unlimited | PD_ | 定期引落 |
| PD_Monthly | PD_ | 定期引落 |
| PD_Notion | PD_ | 定期引落 |
| PD_Parking | PD_ | 定期引落 |
| PD_Rent | PD_ | 定期引落 |
| PD_Train | PD_ | 定期引落 |
| PD_Zaim | PD_ | 定期引落 |
