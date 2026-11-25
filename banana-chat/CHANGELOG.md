# Changelog

Các thay đổi đáng chú ý của **Banana Chat**. Theo [Semantic Versioning](https://semver.org/lang/vi/).

---

## [1.1.2] - 2026-11-25

### Fixed & Added — Hiệu ứng Nhân vật Banana Chat khi Tải tin cũ & Khóa cứng Vị trí Đọc (Chống Nhảy về Đáy)
- **Pill nổi Nhân vật Banana Chat khi cuộn lên tải lịch sử (`MessageThread.vue`, `use-chat.ts`)**: Hiển thị biểu tượng linh vật **Banana Chat** nhún nhảy mượt mà kèm vòng sáng vàng-ngọc và dòng chữ *"Đang tải tin nhắn cũ hơn..."* ngay mép trên khung chat (0px xê dịch bố cục, thời lượng hiển thị tối thiểu `380ms` mượt mắt không chớp giật).
- **Khóa cứng vị trí tin nhắn đang đọc — Chặn dứt điểm lỗi nhảy về tin mới nhất (`MessageThread.vue`, `scroll-anchor-rules.ts`, `ChatView.vue`)**:
  - Neo chính xác tọa độ phần tử DOM (`data-msg-id`) đầu tiên đang nằm trong tầm mắt người dùng trước và sau khi chèn gói tin nhắn cũ, kết hợp khóa tạm `overflow-anchor` để khung nhìn đứng yên tuyệt đối tại dòng đang đọc.
  - Tách riêng `refreshConversationDetail()` cho sự kiện nền `zalo-labels-synced` để không gọi lại `fetchMessages()` làm mất các trang tin cũ vừa tải; hủy ngay các hẹn giờ `scrollToBottom()` khi người dùng chủ động lăn chuột lên; và hiển thị nút nổi **`↓ Có N tin nhắn mới`** khi có tin realtime mới đến lúc đang đọc tin cũ trên cao (đồng bộ cả giao diện Mobile `MobileChatView.vue`).

---

## [1.1.1] - 2026-11-25

### Fixed & Added — Thay sạch Icon chữ `Z` cũ trong `.ico`/PWA & Thêm nút Làm mới (`↻`) trong Cửa sổ App
- **Loại bỏ triệt để logo chữ `Z` cũ (`banana-bubble.ico`, `favicon.ico`, `icon.ico`, `pwa-*.svg`)**: Tái tạo toàn bộ file `.ico` đa độ phân giải (`16x16` → `256x256`) từ hình gốc **Banana Bubble**, xóa toàn bộ file `zalocrm*` / `stepwell*` tồn dư, và cấp mã định danh PWA mới (`id: "/?app=banana-chat"`, `?v=111`) để nút `Open in app` của Chrome/Edge hiển thị đúng biểu tượng quả chuối.
- **Nút Làm mới trang (`↻`) trong cửa sổ App (`DefaultLayout.vue`)**: Khi người dùng đã mở trong chế độ ứng dụng độc lập (`Mở như app` hoặc `Banana-Chat.exe`, nơi không có thanh địa chỉ trình duyệt), vị trí nút *Mở như app* tự động chuyển thành nút **`↻` (Làm mới)** tiện lợi bằng chuột.
- **Biên dịch lại `Banana-Chat.exe` (`v1.1.1`)**: Nhúng biểu tượng `.ico` Banana Chat mới trực tiếp vào file thực thi Windows.

---

## [1.1.0] - 2026-11-25

### Added & Fixed — Nút `Mở như app` (Chuẩn Banana-OS) & Kiểm tra Phiên bản 3 Tầng Không Cần `GITHUB_TOKEN`
- **Nút `🖥️ Mở như app` + Bật/Tắt nhanh âm thanh trên Thanh điều hướng (`DefaultLayout.vue`, `use-pwa-app.ts`, `manifest.json`, `sw.js`)**:
  - Đồng bộ trải nghiệm của **Banana-OS**: người dùng mở trên Chrome/Edge bấm **1 click `Mở như app`** để cài đặt ứng dụng PWA độc lập (tạo icon riêng cho từng tên miền máy chủ khách hàng, không cần biên dịch `.exe` riêng) hoặc mở ngay cửa sổ App gọn không thanh địa chỉ trên mạng LAN HTTP; nút tự động ẩn khi đã ở bên trong cửa sổ App.
  - Bổ sung nút biểu tượng loa `🔊/🔇` ngay cạnh chuông thông báo trên thanh điều hướng để bật/tắt nhanh tiếng chuông tin nhắn mới chỉ với 1 click.
- **Kiểm tra phiên bản mới 3 tầng tự động — Không cần cấu hình `GITHUB_TOKEN` (`update-service.ts`, `publish-release-feed.mjs`)**:
  - **Tầng 1**: GitHub Releases / Contents API (nếu có `GITHUB_TOKEN`).
  - **Tầng 2**: Đọc kênh metadata phiên bản công khai (`duongbirken2510/birken-releases/main/banana-chat/version.json` & `CHANGELOG.md`) — giúp **mọi container Docker và máy chủ khách hàng kiểm tra phiên bản mới tức thì không cần `GITHUB_TOKEN`**, trong khi kho mã nguồn gốc `Banana-Chat` vẫn giữ kín **100% Private**.
  - **Tầng 3**: Tự động đối chiếu `git ls-remote --tags --refs origin` ngay trên máy chủ có kho `.git` (Windows / Linux Git checkout).
  - Hiển thị rõ dòng cấu hình `PLATFORM_ADMIN_EMAILS=<email_đang_đăng_nhập>` trên trang Cập nhật để chủ máy chủ dễ dàng cấp quyền 1-Click.

---

## [1.0.9] - 2026-11-24

### Added & Security — Thông báo & Âm thanh Desktop/Web Phân quyền theo Nick + Nhảy thẳng Hội thoại
- **Phân quyền chặt thông báo tin nhắn đến 2 lớp (`emit-chat.ts` + `use-desktop-notifications.ts`)**:
  - **Lớp 1 (Backend Socket.IO)**: Sự kiện `chat:message` của kênh Zalo nay chỉ phát tới các room `user:${userId}` của nhân sự thực sự có quyền trên nick đó (`owner`/`admin`, chủ nick `ownerUserId`, nhân viên được cấp `ZaloAccountAccess`, và Trưởng/Phó phòng quản lý nhánh phòng ban đó) — chặn dứt điểm rò rỉ nội dung & thông báo nhầm sang máy nhân viên không phụ trách.
  - **Lớp 2 (Frontend + Desktop Bridge v4)**: Gỡ bỏ cơ chế nghe lén `WebSocket` thô trong Rust Desktop App; thay bằng bộ điều phối toàn cục `useDesktopNotifications()` kiểm tra đối chiếu `/api/v1/zalo-accounts` kèm tùy chọn chỉ báo theo *Phạm vi nick đang lọc* trên khung Chat.
- **Cài đặt Bật/Tắt Âm thanh & Popup lưu bền đồng bộ 2 chiều (`PersonalAccountPage.vue` + `main.rs`)**:
  - Bổ sung thẻ **Thông báo & Âm thanh tin nhắn** ngay trong `Cài đặt → Tài khoản của tôi` (gạt bật/tắt Chuông báo, bật/tắt Popup góc màn hình, chọn Phạm vi nick nhận thông báo, nút *Nghe thử & Xem thử*).
  - Trên ứng dụng Desktop (`Banana-Chat.exe`), bổ sung thêm mục bật/tắt Popup dưới khay đồng hồ Windows (System Tray), lưu bền vào `%APPDATA%/vn.banana.chat.crm/desktop-prefs.json` qua các lần tắt mở máy và đồng bộ tức thời 2 chiều với giao diện Web.
- **Bấm thông báo nhảy chính xác 100% tới đúng hội thoại (`/chat/:convId`)**: Nối trực tiếp `activate_from_toast` (từ cả Popup góc màn hình Desktop lẫn Web Notification) vào Vue Router `/chat/:convId` kèm tự động nhận diện chuyển sang đúng nick (`shouldAdoptNickScope`) dù nhân viên đang đứng ở bất kỳ trang nào (`/dashboard`, `/contacts`, `/settings`...).
- **Biên dịch lại `Banana-Chat.exe` (`v1.0.9`)**: Hỗ trợ thêm kết nối trực tiếp tới các máy chủ Self-Hosted mạng nội bộ (`192.168.x.x:7776`, `10.x.x.x`) ngay trong cửa sổ Desktop App.

---

## [1.0.8] - 2026-11-24

### Added & Fixed — Hỗ trợ toàn diện Kho mã nguồn GitHub Private (`GITHUB_TOKEN`)
- **Kiểm tra phiên bản & Changelog cho kho Private (`update-service.ts`)**: Khi khai báo `GITHUB_TOKEN` (quyền `Contents: Read-only`) trong `.env`, hệ thống đọc trực tiếp `VERSION` và `CHANGELOG.md` thông qua GitHub Contents API (`Accept: application/vnd.github.raw+json`) thay vì `raw.githubusercontent.com`; rút ngắn cache lỗi tạm thời xuống 30 giây để nhận ngay sau khi cập nhật `.env`.
- **Sửa hiển thị trạng thái & cảnh báo thiếu `GITHUB_TOKEN` (`SystemUpdatePage.vue`)**: Khi kho GitHub ở chế độ Private mà máy chủ chưa cấu hình `GITHUB_TOKEN` (hoặc token hết hạn/thiếu quyền, GitHub trả `404`/`401`), hiển thị rõ huy hiệu `Cần GITHUB_TOKEN` kèm hướng dẫn cụ thể — dứt điểm lỗi treo chữ *"Đang kiểm tra..."* và không còn báo nhầm *"đang là phiên bản mới nhất"*.
- **Cập nhật Git an toàn không lộ khóa trên CLI (`banana-update.sh`, `updater.mjs`, `update-state.ts`, `.env.example`)**: Tự động truyền xác thực `GITHUB_TOKEN` cho `git fetch` / `git pull` thông qua biến môi trường `GIT_CONFIG_*` (không ghi token vào `.git/config`, không lộ trên `ps aux`), đồng thời redact mọi mẫu `github_pat_*` / `ghp_*` / `Basic` trong nhật ký cập nhật.

---

## [1.0.7] - 2026-11-24

### Security, UX & Polish — Hoàn thiện chu trình v1.0.6 & Tinh chỉnh Trải nghiệm
- **Gắn thông tin người thực hiện (`Dương Birken · duongbirken.deta.vn`)**: Hiển thị tinh gọn tại chân trang Đăng nhập / Thiết lập ban đầu (`LoginView.vue`, `SetupView.vue`) và góc dưới thanh bên Cài đặt / trang Cập nhật hệ thống (`SettingsLayout.vue`, `SystemUpdatePage.vue`) mà không làm rối giao diện trực chat `/chat`.
- **Sửa dứt điểm lỗi bật kép 2 thông báo đỏ khi gọi AI Suggest (`ai-routes.ts`, `ChatContactPanel.vue`, `api/index.ts`)**: Chạy im lặng khi tự động gợi ý ngầm lúc mở Tab CRM (tránh spam thông báo đỏ khi chưa nhập API Key AI); phân loại rõ lỗi cấu hình / khóa AI / lỗi nhà cung cấp ngoài thành mã `400`/`422`/`429` kèm thông điệp tiếng Việt dễ hiểu thay vì `500`, đồng thời bổ sung cờ `_silentErrorToast` / `_globalToastShown` trong Axios interceptor để không bao giờ hiện chồng 2 toast lỗi cùng lúc.
- **Gia cố `NEW-02-CONFIG` & `NEW-02-STALE` (`update-service.ts`, `update-state.ts`, `.env.example`)**: Đặt mặc định `ALLOW_WEB_SELF_UPDATE=false` trong `.env.example`; chuyển `canActorSelfUpdate()` sang **fail-closed** (bắt buộc khai báo tường minh `PLATFORM_ADMIN_EMAILS` khi bật Self-Update); kiểm tra `acquireUpdateLock()` trước `isUpdateInProgress()` và ghi ngay `child.pid` vào `update.lock` sau `spawn()` để tự động mở khóa tức thì khi tiến trình updater cũ bị ngắt thay vì phải chờ hết TTL 15 phút.
- **Gia cố `SEC-12` chống DNS Rebinding & IPv6 Transition (`ssrf-guard.ts`)**: Ghim trực tiếp địa chỉ IP đã vượt kiểm tra DNS vào kết nối `node:https` (`lookup` pinned IP + TLS SNI `servername` + chặn 100% redirect `3xx`) xuyên suốt AI providers, Webhook và Zapier; chặn bổ sung các dải IPv6 nhúng IPv4 (`::ffff:` dạng hex, `64:ff9b::/96` NAT64, `2002::/16` 6to4, `2001:0::/32` Teredo, `::/96`, `100::/64`, `2001:db8::/32`).
- **Hoàn thiện Vận hành & P2 Cleanups**: Thêm persistent volume `banana_system_state:/var/lib/banana-chat/.banana-system` vào `docker-compose.yml` để giữ `previousVersion` bền vững qua recreate container; bật `dotfiles: 'deny', list: false` cho frontend static (`app.ts`); mã hóa URL `spreadsheetId` (`google-sheets.ts`); khóa `https:` + `redirect: 'error'` tại `zinstant-proxy-routes.ts`; dừng an toàn khi thiếu Git tag đích trong `scripts/updater.mjs` và kiểm tra regex `BANANA_IMAGE_TAG` trong `scripts/banana-update.sh`.

---

## [1.0.6] - 2026-11-24

### Security & Stability — Mở khóa chuẩn Staging & Self-Hosted (`REG-01`, `NEW-01`–`NEW-04`, `FB-REDACT`, `SEC-12`)
- **Vá `REG-01` (Tệp khách hàng `P2011`)**: Khôi phục `@default([])` cho `CustomerList.tagRuleNames` và `FriendRequestBatchJob.autoTagNames` kèm migration #120 (`120/120 migrations`, giữ nguyên **0 Prisma schema drift**), truyền tường minh `tagRuleNames: []` tại toàn bộ 4 điểm khởi tạo tệp.
- **Vá `NEW-01` (Cô lập `/files/` & bảo vệ `pg_dump`)**: Dời thư mục trạng thái cập nhật ra ngoài `uploadDir` (`.banana-system`), bật `dotfiles: 'deny'` + `index: false` + `list: false` cho `@fastify/static`, truyền thông số kết nối `pg_dump` hoàn toàn qua biến môi trường (`PGHOST`/`PGPASSWORD`), đặt quyền `0600` cho file `.dump` và tự động redact mọi mật khẩu/token trong `update.log`.
- **Vá `NEW-02` & `NEW-04` (Khóa Self-Update đa tenant & Atomic Lockfile)**: Khóa mặc định `ALLOW_WEB_SELF_UPDATE=false` trong code (bật qua `.env` kèm `PLATFORM_ADMIN_EMAILS` cho máy chủ Self-Hosted), chống race condition TOCTOU bằng atomic lockfile (`wx`) tự động thu hồi stale lock, hỗ trợ checkout đúng tag `vX.Y.Z` trong `updater.mjs` và giới hạn rate-limit cho `changelog?force=1`.
- **Vá `FB-REDACT` (`redact.ts`)**: Sửa dứt điểm lỗi `TypeError` khi đọc hội thoại Facebook (`zaloAccount === null`) trên cả danh sách chat, tin nhắn, Dashboard và AI Suggest; bỏ mock che lỗi trong bộ test.
- **Vá `NEW-03` & `RENDER-ORG` & `SEC-12`**: Gỡ bỏ hoàn toàn secret/UUID cứng cũ tại `system-notify-routes.ts` + bắt buộc `authMiddleware` & `requireGrant('user', 'create')` + ẩn raw error 500; nối `orgId` xuyên qua wrapper `renderTemplate`/`renderTemplateDetailed` (`automation.ts`); bổ sung kiểm tra IP nội bộ sau phân giải DNS (`assertSafeOutboundUrlResolved` trong `ssrf-guard.ts`); kích hoạt chính thức `.github/workflows/`.

---

## [1.0.5] - 2026-11-24

### Changed — Chuẩn hóa Tài liệu & Phát hành GitHub Releases
- **Viết lại toàn bộ `CONTRIBUTING.md` và `SECURITY.md`** dành riêng cho **Banana Chat** (`duongbirken2510/Banana-Chat`), gỡ bỏ toàn bộ liên kết/thông tin dự án cũ.
- **Bổ sung mục Lịch sử phiên bản** vào `README.md` và phát hành đầy đủ **GitHub Releases** kèm Release Notes tiếng Việt chi tiết cho toàn bộ chuỗi phiên bản `v1.0.0` → `v1.0.5`.
- **Cập nhật bài test `v104-system-update.test.ts`** đọc động từ file `VERSION` để tự động tương thích mọi đợt nâng cấp phiên bản sau này.

---

## [1.0.4] - 2026-11-24

### Added — Hệ thống Kiểm tra phiên bản, Thông báo cập nhật & Tự cập nhật (Self-Update + Auto-Rollback)
- **Single Source of Truth (`VERSION` + `CHANGELOG.md`)**: Đồng bộ phiên bản giữa server, frontend, desktop và Docker image; mọi API phản hồi kèm header `X-App-Version`.
- **Kiểm tra phiên bản & Changelog tự động (`GET /api/v1/system/version`, `GET /api/v1/system/changelog`)**: Backend đối chiếu GitHub Releases (cache 6 giờ, có nút kiểm tra ngay), hiển thị chi tiết tính năng mới trước khi bấm cập nhật, kèm danh sách tác vụ đang chạy (`broadcasts`, `groupScans`, `friendBatches`).
- **Trạng thái bền qua restart (`update_state.json`)**: Theo dõi `lastGoodVersion` và `previousVersion` lúc boot trên mọi môi trường (Docker & Native) để luôn biết phiên bản lùi an toàn.
- **1-Click Self-Update + Auto-Rollback (`POST /api/v1/system/update`, `GET /api/v1/system/update/status`, `scripts/updater.mjs`)**: Cho môi trường Git checkout (Windows/Linux/systemd) — tự động backup PostgreSQL (`pg_dump`), stash file tracked bẩn, pull bản mới, build backend/frontend, chạy `prisma migrate deploy`, khởi động lại sạch, poll `/health` + `/version` tối đa 90s và tự động `git reset --hard <oldSha>` nếu bản mới lỗi.
- **Script cập nhật Docker chuẩn cổng 7776 (`scripts/banana-update.sh`)**: Tự động backup DB, kéo/build image mới, migrate, kiểm tra sức khỏe và hỗ trợ rollback nhanh theo tag phiên bản.
- **Giao diện `Cài đặt → Cập nhật hệ thống` + Badge thông báo**: Chấm báo bản mới trên menu Cài đặt cho `owner`/`admin`, thanh tiến trình từng bước realtime, hộp copy lệnh cho Docker, và thanh nhắc nhẹ cho Sale tải lại trang khi server vừa nâng cấp xong.

---

## [1.0.3] - 2026-11-24

### Security & Operations Hardening
- **Vá 5 lỗ hổng P0 (`SEC-01`–`SEC-05`)**: Khóa phân cấp vai trò + thu hồi refresh token + ẩn `tempPassword` khi resend credentials; chuyển `rotateRefreshToken` sang CAS-first; tắt `/api/v1/admin/rbac/create-test-users` trên production; chặn `admin` handoff/bulk-assign lên `owner`; kiểm tra `orgId` trước khi hủy/xóa chiến dịch broadcast trong RAM.
- **Vá P1/P2 (`SEC-06`, `SEC-08`–`SEC-13`)**: Chặn SSRF redirect (`redirect: 'error'`) & ẩn upstream error body trên Webhook/Zapier/AI; sanitize `:href` XSS trên `special-message-renderer.vue`; áp dụng `getZaloScope` + khóa privacy trên `friend-request-batch`; cấu hình `TRUST_PROXY` (mặc định `'loopback'`); hỗ trợ AI trên hội thoại Facebook cho Sale; ẩn raw error ở 41 điểm `status(500)` trên production.
- **Gia cố vận hành (`OPS-01`–`OPS-04`)**: Thêm `workerConnection.on('error')`, bật `--requirepass` cho Redis, khóa bind mặc định `127.0.0.1`, Dockerfile non-root `USER node` + `npm ci` + `exec node`, gỡ `'unsafe-eval'` khỏi Tauri CSP, và đối soát sạch **0 drift** (`119/119 migrations`, `prisma migrate diff --exit-code` = `0`).

---

## [1.0.2] - 2026-11-24

### Fixed
- Sửa lỗi cột `thread_type` trong migration `20260728120000_fix_conversation_thread_type_column` (dứt điểm lỗi `P2022`).
- Sửa lỗi `nameClean` trên Facebook Page connect, thêm `target: ES2022` cho backend, hoàn thiện `requireZaloAccess` & gửi file đính kèm Facebook (`sendFbAttachment`), thêm `prisma/seed.ts`, và tách riêng `test:unit` / `test:integration`.

---

## [1.0.1] - 2026-11-24

### Fixed
- Hoàn thiện migration bổ sung cho Facebook Messenger & AI Agents, vá toàn bộ 14 phát hiện P0 từ báo cáo kiểm thử đầu tiên (cross-tenant Socket.IO room isolation, IDOR AI Agent/Zinstant/Telegram Bridge, Sequence Engine rollover 22h–24h & media-only blocks, XSS, và `GET /health` trả `503` khi mất DB).

---

## [1.0.0] - 2026-11-23

### Added
- Ra mắt **Banana Chat** độc lập trên cổng chuẩn `7776` (Multi-channel Conversational CRM: Zalo + Facebook Messenger + AI Agents + Desktop Cockpit Tauri).

---
