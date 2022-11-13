# Boas_praticas_com_DynamoDB

>Criar uma tabela

aws dynamodb create-table \
    --table-name Musica \
    --attribute-definitions \
        AttributeName=Artista,AttributeType=S \
        AttributeName=TituloMusica,AttributeType=S \
    --key-schema \
        AttributeName=Artista,KeyType=HASH \
        AttributeName=TituloMusica,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5

>Inserir um item

aws dynamodb put-item \
    --table-name Musica \
    --item file://itemmusic.json \        

>Inserir múltiplos itens    

aws dynamodb batch-write-item \
    --request-items file://batchmusic.json

>Criar um index global secundario baseado no título do álbum

aws dynamodb update-table \
    --table-name Musica \
    --attribute-definitions AttributeName=TituloAlbum,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"TituloAlbum-index\",\"KeySchema\":[{\"AttributeName\":\"TituloAlbum\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"    


>Criar um index flobal secundário baseadono nome do artista e no título do álbum            

aws dynamodb update-table \
    --table-name Musica \
    --attribute-definitions\
        AttributeName=Artista,AttributeType=S \
        AttributeName=TituloAlbum,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"ArtistaTituloAlbum-index\",\"KeySchema\":[{\"AttributeName\":\"Artista\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"TituloAlbum\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"


>Criar um index global secundário baseado no título da música e no ano

aws dynamodb update-table \
    --table-name Musica \
    --attribute-definitions\
        AttributeName=TituloMusica,AttributeType=S \
        AttributeName=AnoMusica,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"TituloMusicaAno-index\",\"KeySchema\":[{\"AttributeName\":\"TituloMusica\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"AnoMusica\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"            


>Pesquisar item por artista            

aws dynamodb query \
    --table-name Musica \
    --key-condition-expression "Artista = :artista" \
    --expression-attribute-values  '":artista":{"S":"Lamparina"}}'
aws dynamodb query \
    --table-name Musica \
    --key-condition-expression "Artista = :artista" \
    --expression-attribute-values  '":artista":{"S":"Djonga"}}'
aws dynamodb query \
    --table-name Musica \
    --key-condition-expression "Artista = :artista" \
    --expression-attribute-values  '":artista":{"S":"Lagum"}}'

>Pesquisa pelo index secundário baseado no título da música e no ano

aws dynamodb query \
    --table-name Musica \
    --index-name TituloMusicaAno-index \
    --key-condition-expression "TituloMusica = :v_musica and AnoMusica = :v_ano" \
    --expression-attribute-values  '{":v_musica":{"S":"Não Me Entrego Pros Caretas"},":v_ano":{"S":"2019"} }'
aws dynamodb query \
    --table-name Musica \
    --index-name TituloMusicaAno-index \
    --key-condition-expression "TituloMusica = :v_musica and AnoMusica = :v_ano" \
    --expression-attribute-values  '{":v_musica":{"S":"Bode"},":v_ano":{"S":"2022"} }'
aws dynamodb query \
    --table-name Musica \
    --index-name TituloMusicaAno-index \
    --key-condition-expression "TituloMusica = :v_musica and AnoMusica = :v_ano" \
    --expression-attribute-values  '{":v_musica":{"S":"Deixa"},":v_ano":{"S":"2018"} }'    
