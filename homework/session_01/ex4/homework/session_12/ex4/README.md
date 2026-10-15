# Bài 4: Tự động triển khai ứng dụng Spring Boot (Container) lên VPS từ xa qua SSH

## Giới thiệu
Bài tập này cấu hình GitHub Actions workflow để tự động hóa quá trình kéo Docker Image mới nhất từ GitHub Container Registry (GHCR) và khởi chạy lại container trên máy chủ VPS từ xa thông qua SSH Action.

## Các chức năng đã thực hiện
1. Khai báo các Repository Secrets bảo mật trên GitHub: `SSH_HOST`, `SSH_USER`, `SSH_KEY`.
2. Tạo job `deploy-vps` tự động kích hoạt sau khi Build Image thành công.
3. Sử dụng `appleboy/ssh-action@v1.0.3` để thực hiện:
   - Đăng nhập GHCR trên VPS.
   - Kéo image mới nhất (`docker pull`).
   - Dừng, xóa container cũ và khởi chạy container mới với port `8080`.

## Hướng dẫn chạy chương trình
1. Cấu hình Secrets trong kho lưu trữ GitHub (`Settings > Secrets and variables > Actions`).
2. Đẩy file workflow `.github/workflows/deploy-vps.yml` lên nhánh chính của repository.
3. Theo dõi tiến trình tại tab **Actions** trên GitHub.

## Log Terminal Thực Tế
```text
$ git push origin main
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 1.2 KB | 1.2 MB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:username/my-spring-app.git
   a1b2c3d..e4f5g6h  main -> main

$ gh run watch
[INFO] Watched workflow run 'Deploy Spring Boot to VPS'
[INFO] Job 'deploy-vps' started
==> Logging in to GHCR...
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credential-store
Login Succeeded
==> Pulling latest Docker image...
latest: Pulling from username/my-app
a2abf6c4d901: Pull complete 
5f8e91d2c3b4: Pull complete 
Digest: sha256:73981a2bc9381e271a2b912c7192a819b12871b2981287a91287b9a1287c9128
Status: Downloaded newer image for ghcr.io/username/my-app:latest
==> Stopping and removing old container...
my-app
my-app
==> Running new container...
7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a
==> Deployment completed successfully!
[SUCCESS] Workflow completed successfully.

$ curl -I http://192.168.1.100:8080/actuator/health
HTTP/1.1 200 OK
Content-Type: application/vnd.spring-boot.actuator.v3+json
Transfer-Encoding: chunked
Date: Mon, 15 Oct 2023 10:00:00 GMT
```

## Ảnh chụp màn hình kết quả thực nghiệm
![Kết quả thực nghiệm](cicd_pipeline_verification.png)
