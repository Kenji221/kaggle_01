# データ加工フロー
## タイプを1 hot encodingで分類分け
```python 
out_df = pd.DataFrame()
target_colname = "種類"
target_series = train_df[target_colname]
unique_values = target_series.unique()

for value in unique_values:
    is_value = target_series == value
    out_df[value] = is_value.astype(int)
```

## 駅から徒歩何分かを解釈しやすい形に変える
```python
train_df["最寄り駅1"] = train_df["最寄り駅1"].str.extract(r'(\d+)分')

def impute(var01):
    walk = var01[0]
    if pd.isnull(walk):
        return 10
    else:
        return walk

train_df["最寄り駅1"] = train_df["最寄り駅1"].apply(impute,axis=1)

```

## 住所
```python
train_df[['都道府県',"区"]] = train_bug['住所'].str.split('都|県|道|府|市|区', expand=True).iloc[:, [0, -1]]


State_df = pd.DataFrame()
target_colname = "都道府県"
target_series = train_df[target_colname]
unique_values = target_series.unique()

for value in unique_values:
    is_value = target_series == value
    State_df[value] = is_value.astype(int)
train_df = train_df.join(State_df,how="left")
train_df.drop("都道府県",axis=1,inplace=True)
   
```


## 最寄り２/最寄り３を落とす

## 築年数
train_df["築年数"] = train_df["築年数"].replace("新築","築1年")
train_df['築年数'] = train_df['築年数'].str.extract(r'(\d+)年').astype(int)
## 階数
⇒ - / 0 を含んでいる
　⇒落とす
## 階数建て
# 築年数を年と階建に分割し、欠損値をゼロで置き換える
train_df['築年数_階建'] = train_df['築年数'].str.extract(r'(\d+)階建').astype(int)
⇒ 平屋を含んでいる

## 間取りの分類分け
CountEncodingにて分類分けを行う
```python 
# anime_df["type"].map(anime_df["type"].value_counts())
def create_room_type_count_encoding(input_df):
    count = train_df["間取り"].map(train_df["間取り"].value_counts())
    encoded_df = pd.DataFrame({
        "ID": train_df["ID"],
        "都道府県_Count": count
    })

    return pd.merge(input_df, encoded_df, on ="ID",how="left")
    train_df_02 = create_room_type_count_encoding(train_df)
    assert len(train_df_02)== len(train_df)
    train_df= create_room_type_count_encoding(train_df)


```
