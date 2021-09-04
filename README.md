# Boomkat new release to Spotify playlist

https://boomkat.com/

python spotify api library  
https://spotipy.readthedocs.io/en/2.19.0/



# Deploy Test
## Cloud Build
イメージをビルドする

```
gcloud builds submit --tag gcr.io/[PROJECT_ID]/boomkat-spotify
```

## Set Secret 
Secret Managerにシークレットを登録する
```
printf "[your_secret]" | gcloud secrets create boomkat-sporify --data-file=-
```

確認

```
gcloud secrets versions access 1 --secret boomkat-spotify
```

## Deploy on Cloud Run
Cloud runで環境変数にシークレットとClient IDを渡してデプロイ
```
gcloud beta run deploy boomkat-sporify --image gcr.io/[PROJECT_ID]/boomkat-spotify \
    --update-secrets="SPOTIPY_CLIENT_SECRET=boomkat-spotify:1" \
    --update-env-vars="SPOTIPY_CLIENT_ID=[your_id]" \
```

Secret Managerから環境変数に渡す場合は、Cloud RunのサービスアカウントにSecret Managerへのアクセス権限が必要。


REDIRECT_URIはDashboardから登録が必要。
Cloud Run自身のURL https://<>/をREDIRECT_URIに指定する。

```
gcloud beta run services update boomkat-sporify \
    --update-env-vars="SPOTIPY_REDIRECT_URI=https://<Cloud Run Url>/" 
```

## Localにデプロイ
```
docker run -t boomakt .
```

```
docker run -p 8080:8080 \
    -e "SPOTIPY_CLIENT_ID=[your_id]" \ 
    -e "SPOTIPY_CLIENT_SECRET=[your_secret]" \
    -e "SPOTIPY_REDIRECT_URI=http://127.0.0.1:8080/" \
    boomkat
```