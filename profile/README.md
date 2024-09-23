# Friend Fusion
Welcome to the Friend Fusion organization! Here you can find our projects and contributions.

# Proje Açıklaması

Bu proje, kullanıcıların gerçek zamanlı sohbet edebileceği ve içerik paylaşımında bulunabileceği bir platform sunmaktadır. Kullanıcılar, paylaşım yaptıkları gönderilere yorum yapabilir ve bu yorumlara yanıt verebilirler. **Media** sekmesi altında tüm kullanıcıların paylaştığı gönderileri görebilirken, kullanıcı avatarlarına tıklayarak o kullanıcıların profillerini ziyaret edebilirler. **Meet** sekmesinde ise yeni odalar oluşturup mevcut odalara katılarak sohbet yapma imkanı bulunmaktadır.

## Ana Özellikler
- **Kullanıcı Profili**: Kullanıcılar, üst kısımdaki avatarlarına tıklayarak açılan menüde kendi profillerine, ayarlarına gidebilir veya sistemden çıkış yapabilirler.
- **Gerçek Zamanlı İletişim**: Socket.IO kullanarak anlık sohbet odaları oluşturabilir ve diğer kullanıcılarla iletişim kurabilirler.
- **Post Paylaşımı**: Kullanıcılar, gönderi paylaşıp bunlara yorum yapabilir ve diğer kullanıcıların paylaşımlarını görüntüleyebilirler.

## Projelerdeki Yapı
Tüm repoların içerisinde Dockerfile yer almakta ve aşağıda yer alan YAML dosyasını kullanarak tek bir komut ile tüm projeyi ayağa kaldırabilirsiniz.

### API - api-be
**api-be**, NestJS tabanlı bir API sunmaktadır. Bu API aşağıdaki özellikleri içermektedir:
- **JWT ve 2FA**: Kullanıcı kimlik doğrulaması ve iki faktörlü kimlik doğrulama desteği.
- **Özel Hata Yönetimi**: Özel hata yönetim sistemi ile kullanıcı deneyimi artırılmıştır.
- **Redis Cache Sistemi**: Performans iyileştirmeleri için Redis kullanılmaktadır.
- **AWS S3**: Görsel içeriklerin bulutta saklanması için AWS S3 entegrasyonu.
- **Güvenlik**: Tüm endpointlerde guard kullanılarak erişim güvenliği sağlanmıştır.
- **Loglama**: Tüm istekler ve hata durumları için loglama yapılmaktadır.
- **Veritabanı**: Prisma ORM kullanılarak Neon Console üzerinden veritabanı bağlantıları sağlanmaktadır.

API bağlantılarını gerçekleştirmek için **api-be** dizinine bir `.env` dosyası eklemeniz gerekmektedir. Aşağıda örnek bir `.env` dosyası bulunmaktadır:

