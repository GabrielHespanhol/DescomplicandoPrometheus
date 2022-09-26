# Tipos de dados - Prometheus


### Material de apoio utilizado:
* Documentação do prometheus [Prometheus documentation](https://prometheus.io/docs/practices/histograms/).
* Repositório do curso Descomplicando o prometheus [Link do repositório](https://github.com/badtuxx/DescomplicandoPrometheus).


### Tipos de dados:

##### gauge: Medidor
* O tipo de dado gauge é utilizado para metricas que podem ter seus valores alterados, para cima ou para baixo. Um exemplo de métrica é o uso de CPU ou memório.

```
memory_usage{instance="localhost:8899",job="Primeiro Exporter"}
```

##### counter: Contador
* O tipo de dado counter é o tipo de dado que vai incrementando com o decorrer do tempo, um exemplo é contar quantos erros uma aplicação apresentou na última hora.

```
requests_total{instance="localhost:8899",job="Primeiro Exporter"}
```

##### histogram: Histograma
* O tipo de dado histogram é o tipo de dado que permite especificar o seu valor através de buckets predefinidos. Um exemplo é o tempo em execução onde podemos separar as requisições com base em seu tempo de resposta. A divisão é feita no seguinte formato:

	* Bucket 1: Requisições com tempo de resposta de até 0,5ms
    * Bucket 2: Requisições com tempo de resposta de até 2ms
    * Bucket 3: Requisições com tempo de resposta de até 5ms

Algumas informações importantes sobre o histogram:

* Bucket é onde os valores serão armazenados conforme o intervalo e metricas utilizadas.
* LE é o tamanho do bucket, ex: O bucket 1 tem o LE de até 0,5ms.

```
requests_duration_seconds_bucket{le="0.5"}
```

##### summary : Resumo
* O tipo de dado summary é similar ao histogram, a diferença é que os buckets são chamados de quantiles e são definidos por um valor entre 0 e 1.

```
requests_duration_seconds_sum{instance="localhost:8899",job="Primeiro Exporter"}
```

O ponto alto do summary é a excelente precisão e o ponto baixo é a baixa flexibilidades, pois percentuais e as janelas de tempos precisam ser definidos durante a criação da métrica e não é possível agregar métricas do tipo summary com outras métricas do tipo summary durante a criação das queries.