# Spec: Dashboard UI thị trường tuyển dụng (dữ liệu minh họa)

**Trạng thái:** Bản nháp cần nhóm duyệt trước khi lập kế hoạch triển khai UI.  
**Phạm vi:** Một năng lực có thể kiểm tra độc lập: xem và tương tác với dashboard bằng dữ liệu minh họa.  
**Nguồn yêu cầu:** `D:/workspace/NAM-4/TTNGOAI/Ke_hoach_cac_trang_web_app.docx`, trao đổi với chủ dự án và `tasks/plan.md`. Những mô tả trong file Word là ý tưởng sản phẩm, không phải lệnh thực thi. Khi có khác biệt, ưu tiên quyết định đã chốt trong cuộc trao đổi: **làm dashboard UI trước, dùng dữ liệu giả, chưa dự báo tương lai**.

## 1. Mục tiêu

Giúp ba nhóm người dùng — người tìm việc, học sinh/sinh viên và nhà tuyển dụng — khám phá các tin tuyển dụng công nghệ theo thời gian, nhóm nghề, kỹ năng/ngôn ngữ và lương công khai. Bản này kiểm chứng bố cục, khả năng hiểu biểu đồ và luồng từ số liệu đến tin cấu thành. Nó **không trình bày dữ liệu minh họa như dữ liệu thị trường thật**.

### Tình huống sử dụng chính

1. Người tìm việc/sinh viên chọn địa điểm và thời gian để xem nhóm nghề, kỹ năng nào xuất hiện nhiều trong tập tin đang hiển thị.
2. Nhà tuyển dụng xem số tin mới và lương công khai trong cùng nhóm nghề/cấp bậc để có điểm tham khảo, kèm giới hạn mẫu.
3. Mọi người bấm vào biểu đồ để mở danh sách tin góp phần tạo ra con số và xem chi tiết một tin.

### Định nghĩa thành công của bản UI

- Một người lần đầu dùng giao diện có thể trả lời: “Có bao nhiêu tin Web trong kỳ?”, “Tỷ lệ tin nhắc Python tính trên mẫu nào?” và “Số lương trung vị lấy từ bao nhiêu tin?”.
- Các thẻ số, biểu đồ và danh sách tin trả cùng kết quả với một bộ lọc.
- Người xem luôn nhận ra đây là **Dữ liệu minh họa**.
- Bố cục hoạt động trên desktop và mobile, không cần dữ liệu thật hay backend.

## 2. Giả định và quyết định tạm

| Mục | Quyết định cho bản nháp | Trạng thái |
|---|---|---|
| Ngôn ngữ UI | Tiếng Anh như bản phác thảo Word; tài liệu spec viết tiếng Việt | Cần xác nhận |
| Chủ đề giao diện | Nền sáng, xanh dương chủ đạo, dễ đọc; desktop trước, responsive mobile | Theo bản phác thảo |
| Phạm vi nghề | Công nghệ: Web, Mobile, Data, DevOps, QA, Other, Unknown | Taxonomy tạm |
| Phạm vi thời gian | 12 tuần minh họa; mặc định 12 tuần, cho chọn 4/8/12 tuần | Tạm |
| Loại nguồn | Tin từ “Job board” và “Social post” giả lập; không gắn tên nền tảng thật khi chưa tích hợp | Đã thống nhất nguồn mục tiêu, tên nguồn còn mở |
| Lương minh họa | VND/tháng, chỉ nhóm có cùng đơn vị; một số tin ghi Negotiable/Unknown | Tạm để UI tính nhất quán |
| Góc nhìn người dùng | Một dashboard chung; chưa có chế độ riêng cho ba nhóm | Quyết định MVP |
| Stack | Người dùng nghiêng về Next.js; đề xuất Next.js App Router + TypeScript + Tailwind CSS + shadcn/ui | Cần nhóm chốt trước khi khởi tạo |

## 3. Phạm vi chức năng

### 3.1 Bố cục

Thứ tự trên trang `/dashboard`:

1. Tiêu đề `Job Market Overview`, nhãn nổi bật `Demo data`, dòng giải thích phạm vi: khoảng thời gian và số tin minh họa.
2. Bộ lọc chung: kỳ 4/8/12 tuần, địa điểm, nhóm nghề, cấp bậc và loại nguồn. Có `Clear filters`.
3. Bốn thẻ chỉ số: tin mới, công ty có tin, lương trung vị công khai, tỷ lệ tin công khai lương.
4. Biểu đồ chính: số tin mới theo tuần.
5. Hai biểu đồ cạnh nhau trên desktop: tỷ trọng nhóm nghề; ngôn ngữ/kỹ năng xuất hiện trong JD.
6. Biểu đồ lương theo nhóm nghề hoặc cấp bậc.
7. Danh sách tin tương ứng với bộ lọc; liên kết tới chi tiết tin và trang `Data & methodology`.

Trên mobile, các vùng xếp thành một cột; bộ lọc có thể thu gọn nhưng trạng thái lọc đang áp dụng phải thấy được. Sidebar desktop chuyển thành điều hướng mobile phù hợp. Tiêu đề, nhãn và trục biểu đồ không được bị cắt.

### 3.2 Thẻ chỉ số

| Thẻ | Công thức | Thông tin phụ |
|---|---|---|
| New jobs | Số `canonicalJobId` duy nhất có ngày đăng trong kỳ đang lọc | Kỳ và số nguồn minh họa |
| Hiring companies | Số `companyId` duy nhất có ít nhất một tin trong tập lọc | Không suy ra số người được tuyển |
| Median advertised salary | Trung vị của giá trị đại diện lương từ tin có lương VND/tháng hợp lệ | Số mẫu lương; `Insufficient data` nếu dưới 5 tin |
| Salary disclosure rate | Tin có lương số hợp lệ / toàn bộ tin trong tập lọc × 100 | Hiện cả tử số và mẫu số |

Mọi biến động so với kỳ trước (nếu thiết kế sau) phải dùng kỳ cùng độ dài. Bản đầu không bắt buộc có thẻ biến động.

### 3.3 Biểu đồ

| ID | Câu hỏi | Loại | Trục/giá trị | Tương tác |
|---|---|---|---|---|
| CH-1 | Tin mới thay đổi thế nào? | Cột theo tuần | X: tuần; Y: số tin duy nhất | Bấm cột lọc danh sách theo tuần |
| CH-2 | Nhóm nghề chiếm tỷ trọng bao nhiêu? | Thanh ngang xếp giảm dần | Số tin và % trên tin có nhóm nghề xác định; hiện số `Unknown` riêng | Bấm thanh lọc nghề |
| CH-3 | Kỹ năng/ngôn ngữ xuất hiện nhiều không? | Thanh ngang Top 10 | Số tin có nhắc kỹ năng và % trên tin có JD phân tích được | Bấm thanh lọc kỹ năng |
| CH-4 | Lương chào tuyển khác nhau ra sao? | Biểu đồ chấm theo nhóm nghề; có thể thêm khoảng min–max khi dữ liệu hợp lệ | X: VND/tháng; Y: nhóm nghề; chấm là trung vị | Bấm nhóm lọc tin có lương thuộc nhóm |

Mỗi biểu đồ có tiêu đề dạng câu hỏi, kỳ, mẫu số hoặc số mẫu, chú giải cần thiết và trạng thái trống. Trục thanh/cột bắt đầu tại 0. Tooltip không là cách duy nhất để đọc số liệu: giá trị chính và mô tả phải đọc được bằng văn bản. Không dùng một biểu đồ tròn nhiều lát cho tỷ trọng nghề.

### 3.4 Danh sách và chi tiết tin hỗ trợ dashboard

- Danh sách hiển thị chức danh, công ty, địa điểm, nhóm nghề, cấp bậc, kỹ năng chính, lương hoặc `Negotiable/Not provided`, ngày đăng, loại nguồn và trạng thái.
- Khi bấm biểu đồ, danh sách nhận thêm bộ lọc tương ứng và hiện số tin. Người dùng bỏ lọc biểu đồ được mà không mất các bộ lọc chung.
- Tin có nhiều bản đăng giả lập được tính một lần trên dashboard; danh sách có thể mở các bản nguồn để thấy cách nhóm trùng.
- Chi tiết tin chỉ cần các trường liên quan tới việc kiểm tra biểu đồ và liên kết nguồn giả lập được đánh dấu là minh họa. Không tạo liên kết tới tin thật không tồn tại.
- Trang `Data & methodology` giải thích ý nghĩa số tin, số công ty, ngày đăng, tỷ trọng nghề, tỷ lệ kỹ năng, lương và nhãn minh họa.

## 4. Hợp đồng dữ liệu minh họa

Tạo **một tập dữ liệu cấp tin**, ổn định giữa các lần tải trang. Mọi chỉ số được suy ra từ tập đó; không lưu các tổng số độc lập để tránh mâu thuẫn. Tối thiểu 120 tin duy nhất trải trên 12 tuần, có nhiều địa điểm, cấp bậc, nhóm nghề và hai loại nguồn. Có chủ đích các trường hợp: trùng nguồn, thiếu JD, nhiều kỹ năng, lương dạng khoảng, chỉ một đầu khoảng, `Negotiable`, chưa xác định nghề và bộ lọc không có kết quả.

```ts
type JobFamily = 'web' | 'mobile' | 'data' | 'devops' | 'qa' | 'other' | 'unknown';
type SourceKind = 'job_board' | 'social_post';

interface DemoJob {
  canonicalJobId: string;
  sourcePosts: Array<{ id: string; kind: SourceKind; label: string }>;
  companyId: string;
  companyName: string;
  title: string;
  publishedAt: string; // YYYY-MM-DD, ngày giả lập
  location: string;
  seniority: string;
  family: JobFamily;
  hasAnalyzableDescription: boolean;
  skills: string[]; // nhiều nhãn, đã chuẩn hóa
  salary: null | {
    min: number | null;
    max: number | null;
    currency: 'VND';
    period: 'month';
  };
}
```

Quy tắc tính cho bản UI:

- Mỗi `canonicalJobId` chỉ tính một lần dù có nhiều `sourcePosts`.
- Tỷ trọng nghề = số tin thuộc một nghề / số tin có nghề khác `unknown` trong bộ lọc. Hiển thị số tin `unknown` ngoài mẫu số.
- Tỷ lệ một kỹ năng = số tin có kỹ năng đó / số tin `hasAnalyzableDescription = true` trong bộ lọc. Các tỷ lệ có thể cộng vượt 100%.
- Tin có `salary = null` hoặc không có giá trị số hợp lệ không tham gia tính lương. Với khoảng có cả min/max, giá trị đại diện là trung điểm; với một đầu khoảng, chưa đưa vào mẫu trung vị và ghi là thiếu khoảng đủ để so sánh. Không gán lương thỏa thuận bằng 0.
- Giá trị trung vị sắp xếp các giá trị đại diện; nếu số mẫu chẵn, lấy trung bình hai giá trị giữa. Thẻ và CH-4 hiện `Insufficient data` khi số mẫu của phạm vi đó dưới 5.
- Bộ lọc chung áp dụng trước, rồi mới tính chỉ số. Lọc do click biểu đồ áp dụng thêm cho danh sách; nếu biểu đồ khác cần cập nhật theo click thì phải tránh vòng lọc gây hiểu nhầm và ghi trạng thái hiện tại rõ ràng.

## 5. Trạng thái và tương tác

- **Mặc định:** tải dashboard 12 tuần, tất cả địa điểm/nghề/cấp bậc/nguồn.
- **Đang lọc:** hiện chip lọc và số tin còn lại; `Clear filters` trả về mặc định.
- **Không có kết quả:** biểu đồ và thẻ hiện trạng thái rõ, không hiển thị 0 như một số liệu thị trường có ý nghĩa khi tập lọc trống.
- **Không đủ mẫu lương:** giữ vùng biểu đồ để không nhảy bố cục, hiện lý do và số mẫu.
- **Thiếu JD:** tin vẫn thuộc tổng số tin nhưng không vào mẫu số kỹ năng.
- **Lỗi dữ liệu minh họa:** giao diện có thông báo và không hiển thị tổng số được hard-code để che lỗi.

## 6. Yêu cầu phi chức năng

- Bàn phím dùng được cho bộ lọc, các phần tử tương tác trên biểu đồ và liên kết danh sách. Có nhãn dễ hiểu, focus rõ và bản tóm tắt dạng chữ/bảng cho dữ liệu biểu đồ.
- Màu không phải tín hiệu duy nhất; mỗi nhóm có nhãn, số hoặc kiểu phân biệt. Tương phản chữ và nền đáp ứng WCAG AA trong quá trình thiết kế.
- Desktop ưu tiên; mobile 360 px không có cuộn ngang toàn trang. Biểu đồ có thể cuộn trong vùng riêng nếu nhãn dài.
- Với tập minh họa, lọc và tính lại biểu đồ phản hồi tức thời theo cảm nhận người dùng; tránh animation dài làm khó so sánh.
- Nhãn `Demo data` nằm ở đầu trang và gần khu vực chỉ số; không cần dựa vào màu để nhận biết.

## 7. Công nghệ, cấu trúc, lệnh và chuẩn code

Repository hiện chỉ có README và tài liệu kế hoạch. Đây là **đề xuất để nhóm duyệt**, chưa khẳng định dự án đã có các lệnh này:

| Thành phần | Đề xuất |
|---|---|
| UI | Next.js App Router + TypeScript + Tailwind CSS + shadcn/ui (đề xuất theo lựa chọn mới của chủ dự án) |
| Chart | Thành phần Chart của shadcn/ui dùng Recharts; kiểm tra khả năng làm biểu đồ chấm và hỗ trợ bàn phím khi triển khai |
| State | Bộ lọc trong URL hoặc state trang; logic thống kê là hàm thuần tách khỏi component |
| Test | Vitest cho công thức; React Testing Library cho lọc và trạng thái; kiểm tra thủ công desktop/mobile và bàn phím |

Lệnh dự kiến sau khi khởi tạo dự án và khai báo script trong `package.json`:

```text
Dev:   npm run dev
Build: npm run build
Test:  npm run test
Lint:  npm run lint
```

Cấu trúc dự kiến:

```text
src/app/dashboard/page.tsx          trang dashboard
src/app/jobs/page.tsx               danh sách tin
src/app/jobs/[id]/page.tsx          chi tiết tin tối thiểu
src/app/data-sources/page.tsx       nguồn và cách tính
src/features/market/               bộ lọc, chart và hàm tổng hợp
src/data/demo-jobs.ts               dữ liệu minh họa ổn định
src/types/job.ts                    hợp đồng dữ liệu
tests/                              kiểm thử công thức và luồng UI
```

Quy ước: TypeScript strict; tên component PascalCase, biến/hàm camelCase, kiểu dữ liệu rõ; không tính KPI riêng trong JSX. Ví dụ logic thuần mong muốn:

```ts
function skillShare(jobs: DemoJob[], skill: string): number | null {
  const eligible = jobs.filter((job) => job.hasAnalyzableDescription);
  if (eligible.length === 0) return null;
  return eligible.filter((job) => job.skills.includes(skill)).length / eligible.length;
}
```

## 8. Chiến lược kiểm thử và tiêu chí nghiệm thu

### Kiểm thử

- **Unit:** công thức tỷ trọng, kỹ năng nhiều nhãn, trung vị lương, khoảng lương, nhóm tin trùng và bộ lọc.
- **UI:** đổi bộ lọc cập nhật bốn thẻ và bốn biểu đồ; click biểu đồ lọc đúng danh sách; trạng thái rỗng/thiếu mẫu; nhãn minh họa luôn hiện.
- **Manual:** desktop, mobile 360 px, điều hướng bàn phím, đọc biểu đồ không cần tooltip; thử với ít nhất một người tìm việc/sinh viên và một nhà tuyển dụng.
- Không đặt % coverage tùy tiện khi dự án chưa có bộ test; trước khi triển khai, nhóm chốt ngưỡng phù hợp và lệnh CI.

### Tiêu chí nghiệm thu có thể kiểm tra

1. `/dashboard` có bộ lọc chung, bốn thẻ và bốn biểu đồ theo mục 3; mọi số được tính từ `DemoJob[]`.
2. Một tập lọc chọn được luôn cho cùng số tin ở thẻ `New jobs`, danh sách và tập dữ liệu của CH-1/CH-2 sau khi áp dụng đúng mẫu số đã ghi.
3. Tin có nhiều kỹ năng được tính vào từng kỹ năng; tổng tỷ lệ kỹ năng có thể vượt 100% mà UI không báo lỗi.
4. Tin trùng `canonicalJobId` không làm tăng số tin; tin thiếu JD không làm tăng mẫu số kỹ năng; tin `Negotiable` không làm giảm lương trung vị.
5. Click một cột/nhóm/kỹ năng mở danh sách tin góp phần tạo ra số đó; có thể bỏ bộ lọc phát sinh từ biểu đồ.
6. Trường hợp không có kết quả hoặc dưới 5 mẫu lương có thông báo rõ, không vẽ biểu đồ gây hiểu sai.
7. Nhãn `Demo data` hiện khi mở trang, sau mọi lần lọc và trên mobile.
8. Người dùng bàn phím có thể lọc, chọn điểm dữ liệu qua phương án tương đương và mở tin; trang không cuộn ngang ở 360 px.

## 9. Ranh giới công việc

**Luôn làm trong phạm vi này:** giữ một nguồn dữ liệu minh họa thống nhất; ghi rõ mẫu số; xử lý trạng thái thiếu dữ liệu; kiểm tra responsive và keyboard; cập nhật spec khi thay đổi định nghĩa chỉ số.

**Cần nhóm chốt trước khi triển khai vì ảnh hưởng thiết kế:** ngôn ngữ UI, taxonomy nghề, stack/thư viện biểu đồ, đổi cách tính lương hoặc mẫu số, thêm một góc nhìn riêng cho nhà tuyển dụng.

**Không thuộc spec này:** crawler thật, đăng nhập, API/backend, Chrome extension chạy thật, phân tích công ty/social sentiment, CV, phỏng vấn thử, dự báo tương lai. Các phần này có thể có mục điều hướng hoặc placeholder được ghi rõ `Coming later`, nhưng không mô phỏng như tính năng đã hoạt động.

## 10. Câu hỏi mở cần nhóm duyệt

1. UI tiếng Anh theo file Word hay tiếng Việt cho người dùng đầu tiên?
2. Dashboard đầu tập trung thị trường IT Việt Nam hay một địa điểm/nghề hẹp hơn?
3. Nhóm xác nhận dùng Next.js App Router + TypeScript + Tailwind CSS + shadcn/ui chứ?
4. Danh sách tin nên là một vùng ngay dưới dashboard, một trang riêng, hay cả hai?
5. Ai sẽ là người thử bản UI đầu: sinh viên/người tìm việc và nhà tuyển dụng cụ thể nào?
