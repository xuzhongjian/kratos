# quote-server 接口

### 1、grpc 接口

```bash
grpcurl -plaintext -d '{"currencies": "BTC","quoteCurrency":"USDT"}' \
  10.251.67.192:8085 \
  com.tiger.exodus.quote.QuoteOutService.GetRate
```

