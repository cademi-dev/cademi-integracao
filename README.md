# Cademí Integração

Integre seu próprio gateway com a Cademí

Para liberar suas vendas dentro da Cademí você precisa nos comunicar quando acontecer um evento de venda dentro da sua plataforma.

| Campo | Tipo | Instruções |
| ---- | ---- | ---- |
| token | string | Token da sua integração, disponível em: Plataforma -> Configurações -> Integrações. |
| codigo | string or int | ID da venda na sua plataforma, deve ser único. |
| produto_id | string or int | ID do produto na sua plataforma, o mesmo configurado na Oferta. |
| produto_nome | string | Nome do produto |
| valor | float | Valor da venda [ não obrigatório ] |
| cliente_nome | string | Nome do cliente [ não obrigatório ] |
| cliente_doc | string | CPF ou CNPJ do cliente [ não obrigatório ] |
| cliente_email | string | E-mail do cliente |
| status | string | Possíveis valores: [ aprovado, cancelado, disputa ] |
| recorrencia_id | string or int | ID do contrato de assinatura [ não obrigatório ] |
| recorrencia_status | string | Possíveis valores: [ ativo, cancelado, em-atraso ] |

Exemplo de implementação:

```php
<?php
function sendCademi($domain, $data)
{
    $curl = curl_init($domain.'/api/carga/cademi');
    curl_setopt($curl, CURLOPT_POST, true);
    curl_setopt($curl, CURLOPT_POSTFIELDS, http_build_query($data));
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
    $response = curl_exec($curl);
    $httpcode = curl_getinfo($curl, CURLINFO_HTTP_CODE);
    curl_close($curl);

    switch ($httpcode) {
        case '200':
            $json = json_decode($response);
            return $json->data->carga;
        break;
        case '409':
            $json = json_decode($response);
            throw new \Exception($json->msg);
        break;
        default:
            throw new \Exception("Erro - ".$httpcode);
        break;
    }
}

// Preencha com as informações da venda
$data = [
    'token' => '',
    'codigo' => '',
    'produto_id' => '', 
    'produto_nome' => '',
    'valor' => '',
    'cliente_nome' => '',
    'cliente_email' => '',
    'cliente_doc' => '',
    'status' => '',
    'recorrencia_id' => '',
    'recorrencia_status' => '',
];

// Enviando dados para Cademí
$carga = sendCademi("meu-dominio-plataforma.com.br", $data);

// ID da carga enviada
echo $carga->id;


```
