
<div dir='rtl'>

# فصل چهاردهم انجام تست و خوانایی

</div>

<p align="center">
    <img src="https://github.com/Hossein52Hz/The-Art-Of-Readable-Code-Persian/blob/main/14-Testing-and-Readability/img-14-1.png" />
</p>

<div dir='rtl'>

در این فصل، قصد داریم تکنیک‌های ساده‌ای را برای نوشتن تست‌های مرتب1 و موثر2 نشان دهیم. تست کردن به معنای چیزهای مختلف برای کاربران مختلف است. منظور ما از «تست» در این فصل استفاده از کدی است که تنها هدف آن بررسی رفتار یک قطعه کد دیگر است. هدف ما تمرکز بر روی جنبه خوانایی تست‌ها بوده و به چگونگی نوشتن تست قبل از نوشتن کد واقعی(«توسعه آزمون‌محور3») و یا دیگر جنبه‌های فلسفی توسعه تست، توجهی نداریم.

## خوانایی تست‌های خود را ساده و قابل نگه‌داری کنید

خوانایی کد تست به اندازه خوانایی کدهای غیر تست مهم است. در نگاه کدنویسان غالبا کدهای تست به عنوان مستندات4 غیر رسمی هستند که می‌گوید کد واقعی چگونه کار کرده و چگونه باید مورد استفاده قرار گیرد. بنابراین اگر تست‌ها برای خواندن ساده باشند، کاربران رفتارهای کد اصلی را بهتر می‌توانند درک کنند.

> **_کلید طلایی:_**  کد تست باید قابل خواندن باشد تا دیگر کدنویسان با تغییر آن‌ها یا اضافه کردن تست‌های جدید، راحت باشند.

هنگامی که یک کد تست بزرگ و ترسناک باشد، اتفاقات زیر می‌افتد:

**کدنویسان از تغییر کد واقعی می‌ترسند**، چرا که نمی‌خواهند با این کد دچار آشفتگی شوند. همچنین به‌روزرسانی همه تست‌ها برایشان یک کابوس خواهد شد.

**کدنویسان وقتی کد جدیدی اضافه می‌کنند**، تست جدید اضافه نمی‌کنند. با گذشت زمان، ماژول‌ها کمتر و کمتر تست می‌شوند و دیگر اطمینانی به کار کردن همه آن‌ها وجود نخواهد داشت.

از سوی دیگر، شما می‌خواهید کاربران (به ویژه خودتان!) را تشویق کنید که با تست کردن آن، راحت باشند. آن‌ها باید بتوانند تشخیص دهند که چرا یک تغییر جدید باعث شکست تست موجود می‌شود و همچنین احساس کنند که اضافه کردن تست جدید ساده است.

## مشکل این تست چیست؟!

زمانی در کدپایه ما، تابعی برای مرتب سازی و فیلتر5 کردن لیستی از نتایج جست‌جوی امتیازبندی6 شده وجود داشت که اعلان7 تابع آن به شکل زیر بود:
</div>

```

// Sort 'docs' by score (highest first) and remove negative-scored documents.
void SortAndFilterDocs(vector<ScoredDocument>* docs);

```
<div dir='rtl'>

یک تست برای این تابع، در ابتدا چیزی شبیه زیر است:

</div>

```

void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    docs[0].url = "http://example.com";
    docs[0].score = -5.0;
    docs[1].url = "http://example.com";
    docs[1].score = 1;
    docs[2].url = "http://example.com";
    docs[2].score = 4;
    docs[3].url = "http://example.com";
    docs[3].score = -99998.7;
    docs[4].url = "http://example.com";
    docs[4].score = 3.0;
    SortAndFilterDocs(&docs);
    assert(docs.size() == 3);
    assert(docs[0].score == 4);
    assert(docs[1].score == 3.0);
    assert(docs[2].score == 1);
}

```
<div dir='rtl'>

حداقل هشت اشکال مختلف در این کد تست وجود دارد. در پایان فصل، شما قادر خواهید بود همه این اشکالات را تشخیص و سپس تصحیح کنید.

## تصحیح این تست به شکل خواناتر

به عنوان یک اصل کلی در طراحی، **شما باید جزئیات کم اهمیت را از دید کاربر پنهان کنید تا جزئیات مهم‌تر برجسته‌تر شوند.**

کد تست مذکور به وضوح این اصل را نقض می‌کند. همه جزئیات این تست واضح بوده و مانند دقایق بی اهمیتی که صرف تنظیم vector<ScoredDocument> می‌شود! اکثر خطوط این کد شامل url، score و docs[ ] شده است که فقط جزئیاتی در مورد چگونگی تنظیم شئ‌های1 C++ است و نه درباره اینکه این تست در یک سطح بالا2 چه کاری انجام می‌دهد.

به عنوان اولین قدم در تمیز کردن این مثال، می‌توانیم یک تابع کمکی3 شبیه کد زیر بسازیم:

</div>

```

void MakeScoredDoc(ScoredDocument* sd, double score, string url) {
    sd->score = score;
    sd->url = url;
}

```
<div dir='rtl'>

با استفاده از این تابع، کد تست ما کمی خلاصه‌تر می‌شود:

</div>

```

void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    MakeScoredDoc(&docs[0], -5.0, "http://example.com");
    MakeScoredDoc(&docs[1], 1, "http://example.com");
    MakeScoredDoc(&docs[2], 4, "http://example.com");
    MakeScoredDoc(&docs[3], -99998.7, "http://example.com");
    ...
}

```
<div dir='rtl'>

اما این به اندازه کافی خوب نبوده و هنوز هم جزئیات بی اهمیتی مشاهده می‌شود. به عنوان نمونه، پارامتر«http://example.com» هنگام مشاهده کد، یک چیز ناخوشایند است. این پارامتر همواره یک چیز یکسان است و دقیقا URL در آن هیچ اهمیتی ندارد. چرا که تنها برای معتبر بودن ScoredDocument پر شده است.

یکی دیگر از جزئیات کم اهمیت، که مجبور به دیدن آن بودیم docs.resize(5) و &docs[0]، &docs[1] و غیره است. بیایید تابع کمکی MakeScoredDoc را تغییر دهیم تا کارهای بیشتری برای ما انجام دهد و آن را AddScoredDoc() می‌نامیم:

</div>

```

void AddScoredDoc(vector<ScoredDocument>& docs, double score) {
    ScoredDocument sd;
    sd.score = score;
    sd.url = "http://example.com";
    docs.push_back(sd);
}

```
<div dir='rtl'>

با استفاده از این تابع، کد تست ما فشرده‌تر می‌شود:

</div>

```

void Test1() {
    vector<ScoredDocument> docs;
    AddScoredDoc(docs, -5.0);
    AddScoredDoc(docs, 1);
    AddScoredDoc(docs, 4);
    AddScoredDoc(docs, -99998.7);
    ...
}

```
<div dir='rtl'>

این کد بهتر است، اما هنوز یک تست «بسیار خوانا1 و قابل نوشتن2» نشده است. اگر می‌خواهید تست دیگری با مجموعه‌ای جدید از docs‌های امتیازبندی شده اضافه کنید، این کار نیازمند تعداد زیادی عملیات copy/paste است. حال سوال این است که چگونه می‌توانیم به بهبود آن ادامه دهیم؟

## ساختن حداقل دستورات تست3

برای بهبود کد تست، بیایید از تکنیک فصل دوازدهم، یعنی تبدیل افکار به کد استفاده کنیم. بیایید کاری را که تست ما سعی می‌کند انجام دهد را به زبان ساده توصیف کنیم:

**ما یک لیست از سندهایی داریم که به صورت [-5, 1, 4, -99998.7, 3] امتیازبندی شده‌اند.  پس از SortAndFilterDocs() سندهای باقی مانده باید امتیازهای [4, 3, 1] را به ترتیب داشته باشد.**

همان گونه که می‌بینید، در هیچ قسمتی از این توصیفات، اشاره‌ای به vector<ScoredDocument> نکردیم و آرایه امتیازات مهمترین چیز در اینجا بود. در حالت ایده‌آل، کد تست ما چیزی شبیه به این خواهد بود:

</div>

```

CheckScoresBeforeAfter("-5, 1, 4, -99998.7, 3",  "4, 3, 1");

```
<div dir='rtl'>

ما قادریم که کل این تست را تنها در یک خط کد به طور کامل به اجرا درآوریم! ماهیت اغلب تست‌ها برای بررسی این است که این ورودی/شرایط، این خروجی/ رفتار را از خود نشان دهد که در اکثر مواقع این هدف می‌تواند فقط در یک خط بیان شود. کوتاه نگه داشتن دستورات تست، علاوه بر مختصر و قابل خواندن نمودن آن، سبب راحتی اضافه کردن موارد تست بیشتر می‌شود.

## پیاده سازی سفارشی «Minilanguages»

توجه داشته باشید که CheckScoresBeforeAfter() دو آرگومان به صورت رشته می‌گیرد که آرایه‌ای از امتیازات را توصیف می‌کند. در نسخه‌های جدیدتر C++ شما می‌توانید آرایه‌های لیترال1 را، مانند زیر تعریف کنید:

</div>

```

CheckScoresBeforeAfter({-5, 1, 4, -99998.7, 3}, {4, 3, 1});

```
<div dir='rtl'>

از آنجا که نمی‌توانیم این کار را در لحظه انجام دهیم، امتیازها را درون یک رشته قرار می‌دهیم که با علامت «,» از هم جدا شده‌اند. برای اینکه این روش کار کند، CheckScoresBeforeAfter() باید آرگومان‌های این رشته را تبدیل1 کند.

به طور کلی، تعریف یک زبان کوچک2 سفارشی می‌تواند روشی قدرتمند برای بیان اطلاعات خیلی زیاد در یک فضای کوچک باشد. همچنین می‌توان از printf() و کتابخانه‌های عبارات منظم3 نیز استفاده نمود.

در این مورد، نوشتن بعضی از توابع کمکی برای تجزیه/تبدیل کردن یک لیست از اعداد جدا شده با کاما، نباید کار خیلی سختی باشد. در اینجا CheckScoresBeforeAfter() به صورت زیر است:

</div>

```

void CheckScoresBeforeAfter(string input, string expected_output) {
    vector<ScoredDocument> docs = ScoredDocsFromString(input);
    SortAndFilterDocs(&docs);
    string output = ScoredDocsToString(docs);
    assert(output == expected_output);
}

```
<div dir='rtl'>

که برای کامل شدن آن، یک تابع کمکی برای تبدیل string و vector<ScoredDocument> داریم:

</div>

```

vector<ScoredDocument> ScoredDocsFromString(string scores) {
    vector<ScoredDocument> docs;
    replace(scores.begin(), scores.end(), ',', ' ');
    // Populate 'docs' from a string of space-separated scores.
    istringstream stream(scores);
    double score;
    while (stream >> score) {
        AddScoredDoc(docs, score);
    }
    return docs;
}
string ScoredDocsToString(vector<ScoredDocument> docs) {
    ostringstream stream;
    for (int i = 0; i < docs.size(); i++) {
        if (i > 0) stream << ", ";
        stream << docs[i].score;
    }
    return stream.str();
}

```
<div dir='rtl'>

شاید در نگاه اول این کد خیلی زیاد به نظر برسد، اما کاری را که به شما اجازه می‌دهد انجام دهید، فوق‌العاده قدرتمند است. زیرا شما می‌توانید یک تست کلی را فقط با صدا زدن CheckScoresBeforeAfter() بنویسید و این امر شما را متمایل می‌کند که تست‌های بیشتری بنویسید! همان‌گونه که بعدا در این فصل این کار را انجام خواهیم داد.

## پیام‌های خطا را به شکل خوانا و صحیح بنویسید

<p align="center">
    <img src="https://github.com/Hossein52Hz/The-Art-Of-Readable-Code-Persian/blob/main/14-Testing-and-Readability/img-14-2.png" />
</p>

هر چند کد قبلی خوب بود اما هنگامی که خط assert(output == expected_output) با شکست روبرو شود چه اتفاقی می‌افتد؟ یک پیغام خطا شبیه متن زیر تولید خواهد کرد:

</div>

```

Assertion failed: (output == expected_output),
    function CheckScoresBeforeAfter, file test.cc, line 37.

```
<div dir='rtl'>

بدیهی است که اگر شما تا کنون این خطا را مشاهده نکرده باشید، نگران خواهید شد که مقدارهای output و expected_output() کجا بودند؟

## استفاده از نسخه بهتری از assert() 

خوشبختانه اکثر زبان‌ها و کتابخانه‌ها نسخه‌های سطح بالایی1 از assert() دارند که شما می‌توانید از آن‌ها استفاده کنید. بنابراین به جای نوشتن:

</div>

```

assert(output == expected_output);

```
<div dir='rtl'>

شما می‌توانید از کتابخانه Boost در زبان C++ استفاده کنید:

</div>

```

BOOST_REQUIRE_EQUAL(output, expected_output)

```
<div dir='rtl'>

حال اگر تست دچار شکست شود، پیغامی با جزئیات بیشتر دریافت خواهید کرد، که بسیار مفیدتر است:

</div>

```

test.cc(37): fatal error in "CheckScoresBeforeAfter": critical check
    output == expected_output failed ["1, 3, 4" != "4, 3, 1"]

```
<div dir='rtl'>

در صورت در دسترس بودن، باید از متدهای assertion1 مفیدتر استفاده کنید چرا که در زمان شکست تست، نتایج بهتری خواهد داد.

## توابع ASSERT() بهتر در زبان‌های دیگر

در Python دستور داخلی assert a == b پیغام خطای ساده‌ای را تولید می‌کند:

</div>

```

File "file.py", line X, in <module>
    assert a == b
AssertionError

```
<div dir='rtl'>

شما می‌توانید به جای آن از متد assertEqual() در ماژول unittest استفاده کنید:

</div>

```

import unittest
class MyTestCase(unittest.TestCase):
    def testFunction(self):
        a = 1
        b = 2
        self.assertEqual(a, b)
if __name__ == '__main__':
    unittest.main()

```
<div dir='rtl'>

که پیام خطایی مانند عبارت زیر تولید می‌کند:

</div>

```

File "MyTestCase.py", line 7, in testFunction
        self.assertEqual(a, b)
AssertionError: 1 != 2

```
<div dir='rtl'>

بسته به زبانی که در حال استفاده از آن هستید، احتمالا یک کتابخانه یا فریمورک1(مانند XUnit) برای کمک به شما وجود دارد، که باید هزینه دانستن این کتابخانه‌ها را بپردازید.

## پیام خطاهای دست ساز2

با استفاده از BOOST_REQUIRE_EQUAL()، ما قادر بودیم که پیام خطای بهتری دریافت کنیم:

</div>

```

output == expected_output failed ["1, 3, 4" != "4, 3, 1"]

```
<div dir='rtl'>

با این حال، این پیام می‌تواند بهبود بیشتری پیدا کند.(به عنوان نمونه، می‌تواند برای دیدن ورودی اصلی که باعث این شکست شده بود، مفید باشد). پیام خطای ایده‌آل چیزی شبیه این خواهد بود:

</div>

```

CheckScoresBeforeAfter() failed,
  Input:           "-5, 1, 4, -99998.7, 3"
  Expected Output: "4, 3, 1"
  Actual Output:   "1, 3, 4"

```
<div dir='rtl'>

اگر این همان چیزی است که شما می‌خواهید، رو به جلو حرکت کنید و آن را بنویسید!

</div>

```

void CheckScoresBeforeAfter(...) {
    ...
    if (output != expected_output) {
        cerr << "CheckScoresBeforeAfter() failed," << endl;
        cerr << "Input:           \"" << input << "\"" << endl;
        cerr << "Expected Output: \"" << expected_output << "\"" << endl;
        cerr << "Actual Output:   \"" << output << "\"" << endl;
        abort();
    }

```
<div dir='rtl'>

نکته اخلاقی این قسمت این است که پیام خطا باید تا حد امکان کمک کننده باشد. گاهی، چاپ پیام خودتان با ایجاد یک «assert» سفارشی، بهترین کار برای انجام این کار است.

## انتخاب ورودی‌های خوب برای تست

انتخاب مقادیر ورودی خوب برای تست‌ّهای شما یک هنر است. مواردی که اکنون داریم کمی اتفاقی1 به نظر می‌رسند:

</div>

```

CheckScoresBeforeAfter("-5, 1, 4, -99998.7, 3",  "4, 3, 1");

```
<div dir='rtl'>

چگونه مقادیر خوبی برای ورودی انتخاب کنیم؟ ورودی‌های خوب باید کد را به طور کامل تست کرده و در ضمن باید ساده باشند تا خواندن آن‌ها آسان باشد.

> **_کلید طلایی:_**  شما باید ساده‌ترین مجموعه از ورودی‌هایی که به طور کامل کد را به کار می‌گیرند، انتخاب کنید.

برای مثال، فرض کنید ما فقط این را نوشته بودیم:

</div>

```

CheckScoresBeforeAfter("1, 2, 3", "3, 2, 1");

```
<div dir='rtl'>

اگرچه این تست ساده است، اما رفتار SortAndFilterDocs() را در مورد فیلتر کردن امتیازات منفی تست نمی‌کند. بنابراین اگر اشکالی در این بخش از کد وجود می‌داشت، این ورودی نمی‌توانست متوجه آن شود.
از طرف دیگر، فرض کنید ما خودمان تستی شبیه کد زیر را نوشته بودیم:


</div>

```

CheckScoresBeforeAfter("123014, -1082342, 823423, 234205, -235235",
                       "823423, 234205, 123014");

```
<div dir='rtl'>

این مقادیر غیر مفید، پیچیده هستند و حتی تست کد را به صورت کامل انجام نمی‌دهند.
ساده سازی مقادیر ورودی
برای بهبود مقادیر ورودی چه کاری می‌توانیم انجام دهیم؟


</div>

```

CheckScoresBeforeAfter("-5, 1, 4, -99998.7, 3",  "4, 3, 1");

```
<div dir='rtl'>

احتمالا اولین چیزی که شما متوجه شدید این است که مقدار -99998.7 بسیار برجسته است. از آن جا که این مقدار فقط به معنای «هر عدد منفی» است، بنابراین با یک مقدار ساده‌تر همچون عدد -1 فرقی ندارد(البته اگر مقدار -99998.7 به معنی «یک عدد منفی بسیار بزرگ» بود، یک مقدار بهتر می‌توانست چیزی مانند -1e100 باشد).

> **_کلید طلایی:_**  مقادیر ساده و تمیز تست را که هنوز کار را به درستی انجام می‌دهند ترجیح دهید.
 
مقادیر دیگر در این تست خیلی بد نیستند، اما حالا که اینجا هستیم، می‌توانیم آن‌ها را تا حد امکان به ساده‌ترین عدد صحیح1 کاهش دهیم. همچنین، تنها یک مقدار منفی برای تست کافی است. در اینجا نسخه جدیدی از این تست را می‌توانید ببینید:


</div>

```

CheckScoresBeforeAfter("1, 2, -1, 3", "3, 2, 1");

```
<div dir='rtl'>

ما مقادیر تست را بدون اینکه اثر آن‌ها را کمتر کنیم، ساده‌تر کردیم.

## تست‌های بزرگ «شکننده»1

<p align="center">
    <img src="https://github.com/Hossein52Hz/The-Art-Of-Readable-Code-Persian/blob/main/14-Testing-and-Readability/img-14-3.png" />
</p>

به ازای هر ورودی بزرگ و بی مفهوم یک مقدار دقیق برای تست کردن کد شما وجود دارد. به عنوان نمونه، احتمالا شما وسوسه شده‌اید که تستی شبیه کد زیر را اضافه کنید:

</div>

```

CheckScoresBeforeAfter("100, 38, 19, -25, 4, 84, [lots of values] ...",
                       "100, 99, 98, 97, 96, 95, 94, 93, ...");

```
<div dir='rtl'>

کار خوبی که ورودی‌های بزرگ انجام می‌دهند این است که باگ‌هایی مانند سرریز بافر یا دیگر مواردی که انتظار ندارید رخ دهند را نمایش می‌دهند. اما چنین کدهایی برای مشاهده، بزرگ و ترسناک هستند و به طور کامل در انجام stress-test مفید نیستند. 

## تست‌های چندگانه عملکرد

به جای ساختن یک تک ورودی «عالی» برای تست کامل کد، نوشتن چندین تست کوچک، اغلب ساده‌تر، مفیدتر و دارای خوانایی بیشتری است. هر تست باید در یک بخش مشخص به کد شما ارسال1 شود و در صدد یافتن یک اشکال خاص در آن باشد. به عنوان مثال، در اینجا چهار تست برای SortAndFilterDocs() وجود دارد:

</div>

```

CheckScoresBeforeAfter("2, 1, 3", "3, 2, 1");    // Basic sorting
CheckScoresBeforeAfter("0, -0.1, -10", "0");     // All values < 0 removed
CheckScoresBeforeAfter("1, -2, 1, -2", "1, 1");  // Duplicates not a problem
CheckScoresBeforeAfter("", "");                  // Empty input OK

```
<div dir='rtl'>

اگر می‌خواهید خیلی دقیق باشید، تست‌های بیشتری نیز وجود دارد که می‌توانید بنویسید. داشتن چندین مورد تست جداگانه، کار کردن با کد را برای شخص بعدی ساده‌تر می‌کند. اگر کسی به طور اتفاقی یک باگ را معرفی کند، شکست تست، مورد خاصی که ناموفق بوده است را با دقت نشان خواهد داد. 

## نام‌گذاری توابع تست

کد تست به طور معمول در توابع سازماندهی شده و برای هر متد و/یا شرایطی از یک تست استفاده می‌شود. به عنوان نمونه، کد تست کننده SortAndFilterDocs() در یک تابع، به نام Test1() بود:


</div>

```

void Test1() {
    ...
}

```
<div dir='rtl'>

شاید انتخاب یک نام خوب برای یک تابعِ تست خسته کننده و بی ربط به نظر برسد، اما به هیچ وجه به نام‌های بی معنی مانند Test1()، Test2() و موارد مشابه متوسل نشوید. در عوض، باید از نامی که جزئیات تست را شرح می‌دهد استفاده کنید. به ویژه، جزئیاتی که شخص خواننده به سرعت بتواند درک کند که:

* چه کلاسی(در صورت وجود) دارد تست می‌شود.
* چه تابعی دارد تست می‌شود.
* چه وضعیت یا باگی دارد تست می‌شود.

یک رویکرد ساده برای انتخاب نام خوب برای یک تابع تست، این است که تنها اطلاعات را به یکدیگر الحاق1 کرده و در صورت امکان از یک پیشوند «Test_» استفاده کنید.

به عنوان نمونه به جای نام‌گذاری به Test1() می‌توانیم از قالب Test_<FunctionName>() استفاده کنیم:

</div>

```

void Test_SortAndFilterDocs() {
    ...
}

```
<div dir='rtl'>

بسته به نوع پیچیدگی این تست، احتمالا برای هر موقعیتی که دارد تست می‌شود یک تابع تست جداگانه در نظر می‌گیرید. شما می‌توانید از قالب Test_<FunctionName>_<Situation>() استفاده کنید:


</div>

```

void Test_SortAndFilterDocs_BasicSorting() {
    ...
}
void Test_SortAndFilterDocs_NegativeValues() {
    ...
}
...

```
<div dir='rtl'>

در اینجا از داشتن یک نام ناخوشایند و طولانی نترسید. این تابعی نیست که در کل کدپایه شما فراخوانی شود، بنابراین دلایل اجتناب از نام‌های طولانی برای تابع در اینجا اعمال نمی‌شود. نام تابع تست به طور موثر مانند یک کامنت عمل می‌کند. همچنین در صورت شکست خوردن تست، اکثر فریمورک‌ها نام تابعی که assertion در آن موفق نبوده است را چاپ خواهند کرد، بنابراین نام توصیفی، کمک زیادی خواهد کرد.

توجه کنید که اگر از فریمورک تست استفاده می‌کنید، ممکن است قوانین یا قراردادهایی درباره نحوه نام‌گذاری متدها داشته باشند. به عنوان نمونه در ماژول unittest در زبان Python انتظار می‌رود که نام متدها با کلمه «test» شروع شود. 

هنگامی که صحبت از نام‌گذاری تابع کمکی در کد تست شما می‌شود، برجسته کردن اینکه آیا این تابع assertion‌هایی برای خودش نیز انجام می‌دهد و یا فقط یک «test-unaware» کمکی معمولی است، مفید خواهد بود.

به عنوان نمونه در این فصل هر تابع کمکی که assert() را صدا می‌زند به صورت Check…() نام‌گذاری شده، اما تابع AddScoredDoc() فقط مانند یک تابع کمکی معمولی نام‌گذاری شده است.

## مشکل این تست چیست؟

در ابتدای این فصل، گفتیم که حداقل هشت اشتباه در این تست وجود دارد:

</div>

```


void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    docs[0].url = "http://example.com";
    docs[0].score = -5.0;
    docs[1].url = "http://example.com";
    docs[1].score = 1;
    docs[2].url = "http://example.com";
    docs[2].score = 4;
    docs[3].url = "http://example.com";
    docs[3].score = -99998.7;
    docs[4].url = "http://example.com";
    docs[4].score = 3.0;
    SortAndFilterDocs(&docs);
    assert(docs.size() == 3);
    assert(docs[0].score == 4);
    assert(docs[1].score == 3.0);
    assert(docs[2].score == 1);
}

```
<div dir='rtl'>

اکنون که برخی از تکنیک‌های نوشتن تست‌های بهتر را یاد گرفتیم، بیایید آن‌ها را شناسایی کنیم:

1. این تست بسیار طولانی و پر از جزئیات غیر مهم است. شما می‌توانید در یک جمله توصیف کنید که این کد چه کاری انجام می‌دهد، بنابراین دستور تست نباید خیلی طولانی باشد.
2. افزودن تست جدید ساده نیست و شما وسوسه می‌شوید که از عملیات‌های copy، paste و modify استفاده کنید که این کار باعث طولانی‌تر شدن کد و همچنین پر از تکرار شود.
3. پیام شکست خوردن تست خیلی مفید نیست. اگر این تست با شکست روبرو شود، تنها چیزی که خواهد گفت Assertion failed: docs.size() == 3 است، که به شما اطلاعات کافی برای اشکال زدایی بیشتر را نمی‌دهد.
4. این تست سعی می‌کند که چند چیز را در یک زمان تست کند، یعنی سعی می‌کند تست دو فیلتر منفی و عملیات مرتب سازی را به طور همزمان انجام دهد. اگر این موارد را در تست‌های چندگانه و جدا از هم انجام دهیم، کد تست خوانایی بیشتری خواهد داشت.
5. ورودی‌های تست ساده نیستند. به طور خاص، امتیاز  -99998.7 خیلی پر هیاهو1 است و توجه شما را به خود جلب می‌کند، حتی اگر این مقدار خاص هیچ اهمیتی نداشته باشد. یک مقدار منفی ساده‌تر کفایت می‌کند.
6. ورودی‌های تست به طور کامل با کد ارزیابی نمی‌شوند. به عنوان مثال زمانی که مقدار امتیاز برابر با 0 است را تست نمی‌کند.
7. ورودی‌های دیگری مانند ورودی vector خالی، یک vector بسیار بزرگ یا امتیازات تکراری را تست نمی‌کند.
8. نام Test1() بی معنی است. این اسم باید تابع یا شرایطی را که در حال تست شدن است را  توصیف کند. 

## توسعه Test-Friendly

بعضی از کدها برای تست، ساده‌تر از برخی دیگر هستند. کد ایده‌آل برای تست، دارای یک interface است که به خوبی تعریف شده، وضعیت‌های زیاد یا تنظیمات دیگری نداشته و اطلاعات مخفی زیادی برای رسیدگی ندارد. 

اگر کد خود را در حالی که می‌دانید بعدا برای آن تست خواهید نوشت، بنویسید یک چیز خنده دار اتفاق می‌افتد: شما به گونه‌ای کد خود را طراحی می‌کنید تا تست آن آسان باشد! خوشبختانه، کدنویسی به این شیوه به این معنی است که شما در کل، کد بهتری تولید می‌کنید. به طور طبیعی طراحی‌های سازگار با تست، در اکثر موارد به کدی خوب و سازماندهی شده با قسمت‌های مجزا برای موارد جداگانه منتهی می‌شوند.

## توسعه تست محور یا TEST-DRIVEN DEVELOPMENT

توسعه تست محور(TDD) یک سبک برنامه‌نویسی است که شما قبل از این که کد واقعی خود را بنویسید، تست‌ها را می‌نویسید. طرفداران TDD معتقدند که این فرآیند باعث می‌شود کیفیت کد، بسیار بیشتر از موقعی که تست‌ها را بعد از نوشتن کد می‌نویسید، بهبود یابد. 

این موضوعی چالش برانگیز است و ما نمی‌خواهیم وارد آن شویم، ولی حداقل متوجه شدیم که تنها نگه‌داری تست‌ها در ذهن، در حین نوشتن کد، برای ایجاد کدی بهتر کمک می‌کند.

اما صرف نظر از اینکه TDD را به کار می‌برید یا نه، نتیجه پایانی داشتن کدی است که دیگر کد‌ها را تست می‌کند. هدف این فصل این است که به شما کمک کند، تست‌های ساده‌تری برای خواندن و نوشتن ایجاد کنید.

در بین تمام راه‌های شکستن یک برنامه به کلاس‌ها و متدها، معمولا جداشدنی‌ترین آن‌ها، ساده‌ترین گزینه برای تست هستند. از طرف دیگر، برنامه شما با تعداد زیادی فراخوانی متد در بین کلاس‌ها، همراه با تعداد زیادی پارامتر برای متد‌ها، بسیار بهم پیوسته و پیچیده شده است که نه تنها فهمیدن کد برنامه را سخت، بلکه کد تست نیز زشت و خواندن و نوشتن آن دشوار خواهد شد.

داشتن تعداد زیادی کامپوننت خارجی(یعنی متغیرهای سراسری که باید مقداردهی شوند، کتابخانه‌ها یا فایل‌های پیکربندی2 که باید بارگیری3 شوند و غیره) نیز نوشتن تست‌ها را آزاردهنده‌تر4 می‌کند.

به طور کلی اگر در حال طراحی کد بوده و متوجه شدید که: این برای تست کردن یک کابوس خواهد بود! همین دلیل خوبی است که دست نگه داشته و مجددا به طراحی فکر کنید. جدول ۱-۱۴ برخی از مشکلات تست‌های معمولی و طراحی آن‌ها را نشان می‌دهد.


| مشکل طراحی                                                                                                                                                                                                                                                           | مشکل تست کردن                                                                                                                                        | مشخصه                                    |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|-----------------------------------------------------------------------------------------------------------------------------------------------------:|-----------------------------------------:|
| درک اینکه کدام توابع دارای چه اثرات جانبی است، سخت است. نمی‌توان درباره هر تابع به صورت جداگانه فکر کرد. برای درک اینکه آیا همه چیز کار می‌کند، باید کل برنامه را در نظر بگیرید.                                                                                     | همه وضعیت‌های سراسری باید برای هر تست ریست شوند(در غیر این صورت، تست‌های مختلف می‌توانند با یکدیگر تداخل داشته باشند).                               | استفاده از متغیرهای سراسری               |
| هنگامی که یکی از وابستگی‌ها1 با شکست روبرو شود، سیستم به احتمال زیاد شکست خواهد خورد. درک اینکه این تغییر ممکن است چه اثراتی بگذارد، دشوارتر است. بازسازی کردن کلاس‌ها کار سخت‌تری است. حالت‌های شکست سیستم بیشتر و بازیابی مسیر برای فکر کردن درباره آن سخت‌تر است. | نوشتن هرگونه تستی دشوار است، زیرا تعداد کارهای مقدماتی زیاد است و نوشتن تست‌ها کمتر سرگرم کننده‌اند، بنابراین افراد از نوشتن تست‌ها خودداری می‌کنند. | وابستگی کد به تعداد زیادی کامپوننت خارجی |
| این برنامه به احتمال زیاد دارای شرایط خاص(رقابت) یا دیگر باگ‌های غیرقابل تکرار است. استدلال درباره این برنامه سخت است. ردیابی و رفع باگ‌ها در محصول بسیار دشوار خواهد بود.                                                                                           | تست‌ها شکننده و غیرقابل اعتماد هستند. تست‌هایی که گاهی شکست می‌خورند در نهایت نادیده گرفته می‌شوند.                                                  | رفتار غیرقطعی1 کد                        |



در سوی دیگر، اگر یک طراحی داشته باشید که تست نوشتن برای آن ساده باشد، این یک نشانه خوب است. جدول ۲-۱۴ برخی از مزیت‌های تست و طراحی مشخصه‌ها را لیست کرده است.


| مزیت طراحی                                                                                                                 | مزیت تست‌پذیری                                                                                             | مشخصه                                                         |
|---------------------------------------------------------------------------------------------------------------------------:|-----------------------------------------------------------------------------------------------------------:|--------------------------------------------------------------:|
| درک کلاس‌ها با وضعیت‌های کمتر، ساده‌تر و راحت‌تر است.                                                                      | نوشتن تست‌ها ساده‌تر است زیرا تنظیمات کمتری برای تست یک متد و حالت پنهان کمتری برای رسیدگی وجود دارد.      | کلاس‌ها بدون وضعیت داخلی و یا دارای تعداد کمی از آن‌ها هستند. |
| کامپوننت‌های کوچک‌تر یا ساده‌تر قابلیت ماژولاریتی بیشتری دارند و سیستم در کل، جداسازی بیشتری دارد.                         | برای تست کامل، test case کمتری نیاز است.                                                                   | کلاس‌ها/توابع تنها یک کار انجام می‌دهند.                      |
| سیستم می‌تواند به شکل موازی توسعه داده شود، کلاس‌ها می‌تواند بدون مختل کردن بقیه سیستم، به راحتی تغییر کرده و یا حذف شوند. | هر کلاس می‌تواند به شکل مستقل تست شود(خیلی ساده‌تر از تست کردن چندین کلاس به طور هم زمان)                  | کلاس‌ها به کلاس‌های کمتری وابسته هستند(یعنی حداکثر جداسازی)   |
| یادگیری و استفاده مجدد interface‌ها برای کدنویسان ساده‌تر است.                                                             | رفتارهایی که به خوبی تعریف شده‌اند، جهت تست وجود دارند. interface‌های ساده، کار کمتری برای تست نیاز دارند. | توابع، interface‌های ساده‌ای دارند که به خوبی طراحی شده‌اند.  |



## جزئیات بیشتر

این امکان پذیر است که تمرکز خیلی بیشتری روی تست شود. در اینجا چند مثال داریم:

* **قربانی کردن خوانایی کد اصلی به دلیل فعال کردن تست‌ها.** طراحی کد اصلی شما به صورت تست‌پذیر، باید دارای یک شرایط برد-برد باشد یعنی کد اصلی ساده‌تر و دارای جداسازی بیشتری است و تست‌ها نیز برای نوشتن راحت هستند. این کار اشتباهی است که تعداد زیادی اتصالات1(پیچیدگی) در کد اصلی خود برای تست کردن آن‌ها اضافه کنید.
* **در مورد پوشش ۱۰۰ درصدی تست وسواس داشته باشید.** تست ۹۰درصد ابتدای کد، اغلب کمتر از تست ۱۰ درصد آخر وقت گیر است. آن ۱۰ درصد احتمالا شامل رابط کاربری2 یا موارد خطای گنگ می‌شود، یعنی مواردی که هزینه باگ در آن‌ها خیلی زیاد نیست و ارزشی برای تست کردن ندارد. اما حقیقت این است که شما هرگز پوشش ۱۰۰ درصدی، نخواهید داشت. 
* اگر یک باگ هم از دست نرفته است، احتمالا یک ویژگی از دست رفته یا شما متوجه نشده‌اید که آن مشخصه باید تغییر می‌کرده است. بسته به اینکه باگ‌های شما چقدر هزینه‌بر هستند، این مهم است که چقدر از زمان توسعه را برای تست کردن کد هزینه کنید. اگر در حال ساخت یک نمونه اولیه وبسایت هستید، احتمالا به هیچ وجه ارزش نوشتن کد تست را نداشته باشد. از طرف دیگر، اگر شما یک کنترل کننده برای یک سفینه فضایی یا وسیله پزشکی نوشته‌اید، بی شک تمرکز اصلی شما باید تست کردن آن باشد.
* **اجازه دهید انجام تست در مسیر توسعه محصول باشد.** ما با موقعیت‌هایی روبرو شده‌ایم که به جای آن که تست، فقط یکی از جنبه‌های پروژه باشد، به کل پروژه حاکم شده است. گاهی اوقات تشریفات عملیات تست بیش از حد مورد توجه قرار می‌گیرد و کدنویسان متوجه نمی‌شوند که می‌توانند وقت گرانبهایشان را درجای بهتری صرف کنند.

## خلاصه فصل

در کد تست، خوانایی خیلی مهم است. اگر تست‌های شما خیلی خوانا باشند، به نوبه خود بسیار قابل نوشتن خواهند بود و به همین دلیل تعداد افراد بیشتری از آن‌ها استفاده و به آن‌ها تست‌های جدید اضافه می‌کنند. همچنین، اگر کد اصلی را برای تست، ساده طراحی کرده‌اید، در کل، کد شما طراحی بهتری خواهد داشت.

در اینجا نکات خاصی در مورد اینکه چگونه تست‌های خود را بهبود دهید، داریم:

* سطح بالای هر تست باید تا حد امکان مختصر باشد. در حالت ایده‌آل، هر ورودی/خروجی تست می‌تواند تنها در یک خط از کد شرح داده شود.
* اگر تست شما با شکست مواجه شد، باید پیام خطایی منتشر کند که باعث شود ردیابی و تصحیح باگ ساده باشد.
* از ساده‌ترین ورودی‌های تست که به طور کامل کد شما را ارزیابی می‌کنند استفاده کنید.
* به توابع تست خود یک اسم کاملا توصیفی بدهید تا مشخص باشد هر کدام از آن‌ها چه چیزی را تست می‌کند. به جای Test1() از نامی مانند Test_<FunctionName>_<Situation> استفاده کنید.
* و مهم‌تر از همه اینکه، اصلاح و اضافه کردن تست‌های جدید را آسان کنید.

</div>

<div>
[1]: 
<br>
[2]: 
<br>
[3]: 
<br>
[4]: 
<br>
[5]: 
<br>
[6]: 
<br>
[7]: 
<br>
[8]: 
<br>
[9]: 
<br>
[10]: 
<br>
[11]: 
<br>
[12]: 
<br>
[13]: 
<br>
[14]: 
<br>
[15]: 
<br>
[16]: 
<br>
[17]: 
<br>
[18]: 
<br>
[19]: 
<br>
[20]: 
<br>
[21]: 
<br>
[22]: 
<br>
[23]: 
<br>
[24]: 
<br>
[25]: 
<br>
[26]: 
<br>
[27]: 
<br>
[28]: 
</div>
