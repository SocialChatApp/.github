[YouTube Video: Trailer](https://www.youtube.com/watch?v=1pF_iDZWcmU)

<details>
<summary><strong>Friend Fusion</strong> (Türkçe)</summary>
Hoşgeldiniz.

# Proje Açıklaması

Bu proje, kullanıcıların gerçek zamanlı sohbet edebileceği ve içerik paylaşımında bulunabileceği bir platform sunmaktadır. Kullanıcılar, paylaşım yaptıkları gönderilere yorum yapabilir ve bu yorumlara yanıt verebilirler. **Media** sekmesi altında tüm kullanıcıların paylaştığı gönderileri görebilirken, kullanıcı avatarlarına tıklayarak o kullanıcıların profillerini ziyaret edebilirler. **Meeting** sekmesinde ise yeni odalar oluşturup mevcut odalara katılarak sohbet yapma imkanı bulunmaktadır.

## Ana Özellikler
- **Kullanıcı Profili**: Kullanıcılar, üst kısımdaki avatarlarına tıklayarak açılan menüde kendi profillerine, ayarlarına gidebilir veya sistemden çıkış yapabilirler.
- **Gerçek Zamanlı İletişim**: Meeting sayfasına giderek anlık sohbet odaları oluşturabilir, mevcut odalara katılabilir ve diğer kullanıcılarla iletişim kurabilirler.
- **Post Paylaşımı**: Kullanıcılar, gönderi paylaşıp bunlara yorum yapabilir ve diğer kullanıcıların paylaşımlarını görüntüleyebilirler.

### Back-End 
**api-be**, NestJS tabanlı bir API sunmaktadır. Bu API aşağıdaki özellikleri içermektedir:
- **JWT ve 2FA**: Kullanıcı kimlik doğrulaması ve iki faktörlü kimlik doğrulama desteği.
- **Custom Error Handling**: Özel hata yakala sistemi ile kullanıcı deneyimi ve projenin geliştirilebilirliği artırılmıştır.
- **Redis Cache Sistemi**: Performans iyileştirmeleri için Redis kullanılmaktadır. Verify token kullanıcılara özel mail key and token olarak redis üzerinde tutulmaktadır.
- **AWS S3**: Görsel içeriklerin bulutta saklanması için AWS S3 entegrasyonu.
- **Güvenlik**: Tüm endpointlerde guard kullanılarak erişim güvenliği sağlanmıştır.
- **Loglama**: Tüm istekler ve hata durumları için loglama yapılmaktadır. Logs klasörü altından buna ulaşabilirsiniz.
- **Veritabanı**: Prisma ORM kullanılarak Neon Console üzerinden veritabanı bağlantıları sağlanmaktadır.
- **Mailer**: Nodemailer sayesinde 2FA kısmında kullanıcılara kayıt olabilmeleri için gerekli 4 haneli kodu gönderebilme imkanı sağlamaktadır.

```typescript
import { Injectable } from '@nestjs/common';
import * as nodemailer from 'nodemailer';

@Injectable()
export class MailerService {

    private transporter;

    constructor() {
        this.transporter = nodemailer.createTransport({
            service: 'gmail',
            auth: {
                user: 'your-gmail',
                pass: 'your-pass-2fa',
            },
        });
    }

    async sendMail(to: string, subject: string, text: string) {
        const mailOptions = {
            from: 'your-gmail',
            to,
            subject,
            text,
        };

        const info = await this.transporter.sendMail(mailOptions);
    }
}
```

API bağlantılarını gerçekleştirmek için **api-be** dizinine bir `.env` dosyası eklemeniz gerekmektedir. Aşağıda örnek bir `.env` dosyası bulunmaktadır:

```
DATABASE_URL="your-database-connection-string"

AWS_ACCESS_KEY_ID=your-aws-key
AWS_SECRET_ACCESS_KEY=your-aws-secret-key
AWS_S3_REGION=your-aws-region
AWS_S3_BUCKET_NAME=your-aws-s3-bucket-name

REDIS_HOST="redis"
REDIS_PORT="6379"
```


### Front-End 
**app-fe**, ReactJS ve Vite kullanılarak geliştirilmiş bir ön yüz uygulamasıdır. Aşağıdaki kütüphaneler kullanılmaktadır:
- **Lottie Animasyonları**: Kullanıcı deneyimini zenginleştiren animasyonlar.
- **Redux Toolkit**: Durum yönetimi için Redux kullanımı.
- **Axios**: API ile iletişim için Axios kullanılmaktadır.
- **Material UI**: Kullanıcı arayüzü bileşenleri için Material UI.
- **React Router DOM**: Sayfalar arası yönlendirme ve guard kontrolleri.

Router konfigürasyonları sayesinde, kullanıcı giriş yapmadığında otomatik yönlendirme yapılmakta; giriş yapmış kullanıcıların girebildiği sayfalara erişim kısıtlanmaktadır. Kullanıcılar, register veya login işlemi yaptıklarında verileri cookie olarak kaydedilir ve 7 gün boyunca tutulur. Access token geçerliliği sona erdiğinde, kullanıcı oturum zaman aşımına uğradığı bilgisi alır ve login sayfasına yönlendirilir.

### Socket Server
**Server** repository'si, NestJS ile Socket.IO sunucusunu barındırmaktadır. **app-fe** ve **server** arasında iletişim sağlanmaktadır. Kullanıcılar, **meeting** sayfasına girdiklerinde lobby'e giriş yapar ve bilgiler sunucuya iletilir. Burada tüm bilgiler tutulmakta, oda oluşturma ve odalara katılma işlemleri gerçekleştirilebilmektedir.

## Port Yapılandırmaları
- **api-be**: 3000 
- **app-fe**: 5173 
- **server**: 4000 
- **Redis**: 6380 

Docker-Compose-Yaml üzerinde Redis için port binding yapılmaktadır.


## Docker Compose Yaml Dosyası

```
services:
  api-be:
    build:
      context: ./api-be
      target: development
    container_name: api-be
    ports:
      - "3000:3000"
    depends_on:
      - redis
      - rabbitmq
    networks:
      - backend 
    volumes:
      - ./api-be:/app

  socket-server:
    build:
      context: ./server
    container_name: socket-server
    ports:
      - "4000:4000"
    networks:
      - backend

  app-fe:
    build:
      context: ./app-fe
    container_name: app-fe
    ports:
      - "5173:5173"
    depends_on:
      - api-be
    networks:
      - backend

  redis:
    image: "redis:6.2-alpine"
    container_name: redis
    restart: always
    ports:
      - "6379:6379"
    command: redis-server  --save 20 1 --loglevel warning
    volumes:
      - redis:/data
    networks:
      - backend

  rabbitmq:
    image: "rabbitmq:3-management"
    container_name: rabbitmq
    ports:
      - "5672:5672"
      - "15672:15672"
    volumes:
      - rabbitmq_data:/var/lib/rabbitmq
    networks:
      - backend

volumes:
  redis:
  rabbitmq_data:

networks:
  backend:
    name: backend
    driver: bridge
```
</details>