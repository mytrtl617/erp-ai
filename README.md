# AI Electronics ERP — פרויקט גמר

מערכת ERP מבוססת AI לעסק אלקטרוניקה ישראלי דמיוני ("איי.איי אלקטרוניקה"). המערכת בנויה משלוש שכבות:

- **Airtable** — מקור הנתונים (4 טבלאות: Invoices, Leads, Products, Tasks)
- **n8n** — 10 workflows שמריצים את כל הלוגיקה העסקית והסוכנים
- **Lovable (App)** — ממשק ניהול פנימי שמדבר עם n8n דרך webhook יחיד

## מבנה הבסיס (Airtable)

| טבלה | שדות |
|---|---|
| Invoices | InvoiceNumber, CustomerId, Amount, VatAmount, Total, Status, PdfUrl, Created |
| Leads | Name, Email, Company, Status, Created |
| Products | Name, Category, Price, Description, InStock |
| Tasks | Title, Status |

ראו `schema.json` לפרטים המלאים.

## ה-10 Workflows (תיקיית `workflows/`)

| # | קובץ | תפקיד |
|---|---|---|
| 1 | Tax Validation | מוודא חשבונית חדשה, מחשב מע"מ (17%/18% לפי תאריך), נותן מספר סידורי |
| 2 | Leads Intake | קולט ליד חדש מ-webhook, בודק כפילויות, כותב ל-Airtable |
| 3 | Sales Cold Emails | כל 3 שעות שולח מייל מכירה ללידים חדשים בעזרת AI |
| 4 | Sales Reply | כל 30 דק' בודק תשובות ב-Gmail ומעדכן סטטוס ליד |
| 5 | Customer Service | סוכן AI בטלגרם עם RAG (מדיניות + מוצרים) לשירות לקוחות |
| 6 | Policies Embedding | טוען מסמכי מדיניות (md) למאגר וקטורי |
| 7 | Products Embedding | טוען קטלוג מוצרים (CSV) למאגר וקטורי |
| 8 | InvoiceMaker | הופך חשבונית מאושרת ל-HTML, מעלה ל-Google Drive |
| 9 | Manager Agent | סוכן AI בטלגרם לבעל העסק, עם כלי Airtable לשליפת נתונים |
| 13 | App | Webhook יחיד שהאפליקציה מדברת דרכו (read/create/update/chat) |

## הפעלה

1. ייבאו כל קובץ מ-`workflows/` ל-n8n (Import from File).
2. בכל צומת Airtable בחרו מחדש Base + Table מהבסיס שלכם.
3. חברו credentials (Airtable, OpenAI, Gmail, Telegram×2, Google Drive).
4. הריצו ידנית פעם אחת את #6 ו-#7 (RAG).
5. הפעילו (Activate/Publish) את שאר 8 ה-workflows.
6. חברו את האפליקציה ל-webhook production URL של #13.

## מגבלות ידועות

- המאגר הווקטורי (RAG) בזיכרון — נמחק בכל restart של n8n, צריך להריץ מחדש #6/#7.
- מספור החשבוניות עלול להתנגש בריצה מקבילית (אתגר פתוח).
- אין המרת PDF אמיתית — המסמך נשמר כ-HTML.
