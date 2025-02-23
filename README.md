To **print your `invoice.cshtml` view as a PDF** using only **Microsoft-supported tools** without third-party libraries like iTextSharp or RDLC, you can use the **browser’s print functionality** through **JavaScript** and the **C# Response object** to trigger PDF generation.

---

### ✅ **Solution 1: Using JavaScript (Browser-Based Print to PDF)**

1. **In your `invoice.cshtml` file, add a print button:**

```html
<button onclick="printInvoice()">Download PDF</button>

<div id="invoiceContent">
    <h2>Invoice #12345</h2>
    <p>Customer: John Doe</p>
    <p>Total: $150.00</p>
</div>

<script>
    function printInvoice() {
        var printContents = document.getElementById('invoiceContent').innerHTML;
        var newWindow = window.open('', '', 'width=800,height=600');
        newWindow.document.write(`
            <html>
                <head>
                    <title>Invoice PDF</title>
                    <style>
                        /* Basic styling for print */
                        body { font-family: Arial, sans-serif; margin: 40px; }
                        h2 { text-align: center; }
                    </style>
                </head>
                <body>
                    ${printContents}
                </body>
            </html>
        `);
        newWindow.document.close();
        newWindow.focus();
        newWindow.print();
        newWindow.close();
    }
</script>
```

This will open the **browser’s print dialog**, allowing users to select **“Save as PDF”**.

---

### ✅ **Solution 2: Using C# — Force Browser to Render PDF Format**

If you want to render the **HTML as a PDF** directly using **C#**, you can utilize the **`Response`** object.

1. **Controller Code Example:**

```csharp
public ActionResult DownloadInvoicePDF()
{
    string htmlContent = System.IO.File.ReadAllText(Server.MapPath("~/Views/Invoice/invoice.cshtml"));

    Response.Clear();
    Response.ContentType = "application/pdf";
    Response.AddHeader("content-disposition", "attachment;filename=Invoice.pdf");
    Response.Write(htmlContent);
    Response.End();

    return new EmptyResult();
}
```

2. **What Happens?**
   - This forces the browser to treat the rendered HTML as a PDF.
   - The user can then use the browser’s **Print → Save as PDF** option.

---

### ✅ **Which to Use?**

| Method                 | Approach                  | Best For                         |
|------------------------|---------------------------|-----------------------------------|
| **JavaScript Print**    | Client-Side (Simple)      | Quick print/download by users    |
| **C# Response**         | Server-Side               | Auto-downloads raw PDF content   |

💡 **Tip:** Using the browser’s built-in **“Save as PDF”** through JavaScript is the **simplest** method without third-party libraries.  

Does this match your requirements?
-----------------------------------------------------------------------------------------------------
Since your **`invoice.cshtml`** uses a **layout** and a **model**, and you still want to generate a PDF using **only Microsoft-supported libraries**, here's an adapted solution that preserves the layout and model while enabling the download as a PDF.

We’ll focus on rendering the full view (with layout and model) and then triggering the browser’s **Print → Save as PDF** functionality or printing directly to PDF using JavaScript.

---

## ✅ **Updated Step-by-Step Approach**

### **1️⃣ Invoice View (`invoice.cshtml`)**

Use the standard layout and bind your model as usual:

```csharp
@model YourNamespace.Models.InvoiceModel
@{
    ViewBag.Title = "Invoice";
    Layout = "~/Views/Shared/_Layout.cshtml"; // Keep layout
}

<h2>Invoice #@Model.InvoiceNumber</h2>
<p><strong>Customer:</strong> @Model.CustomerName</p>
<p><strong>Date:</strong> @Model.InvoiceDate.ToShortDateString()</p>

<table class="table table-bordered">
    <thead>
        <tr>
            <th>Item</th>
            <th>Qty</th>
            <th>Price</th>
            <th>Total</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in Model.Items)
        {
            <tr>
                <td>@item.Name</td>
                <td>@item.Quantity</td>
                <td>@item.Price.ToString("C")</td>
                <td>@(item.Quantity * item.Price).ToString("C")</td>
            </tr>
        }
    </tbody>
    <tfoot>
        <tr>
            <td colspan="3"><strong>Grand Total</strong></td>
            <td><strong>@Model.Items.Sum(i => i.Quantity * i.Price).ToString("C")</strong></td>
        </tr>
    </tfoot>
</table>

<button id="printInvoice" class="btn btn-primary">Print/Save as PDF</button>
```

---

### **2️⃣ JavaScript for PDF Generation (Client-Side Print Method)**

Add this script inside your layout or directly in the view:

```html
<script>
    document.getElementById("printInvoice").addEventListener("click", function () {
        // Open a new window for the printable version
        var printContents = document.body.innerHTML; // or isolate the specific div for the invoice
        var printWindow = window.open('', '', 'height=800,width=800');
        printWindow.document.write('<html><head><title>Invoice PDF</title>');
        printWindow.document.write('<link rel="stylesheet" href="/Content/bootstrap.css" />'); // Include styles if needed
        printWindow.document.write('</head><body>');
        printWindow.document.write(printContents);
        printWindow.document.write('</body></html>');
        printWindow.document.close();
        printWindow.focus();
        printWindow.print();
        printWindow.close();
    });
</script>
```

---

### **3️⃣ Controller to Load Model (if needed)**

If the invoice data comes from the controller, use this:

```csharp
public ActionResult Invoice(int id)
{
    // Mocked data for demo
    var model = new InvoiceModel
    {
        InvoiceNumber = "INV123",
        CustomerName = "John Doe",
        InvoiceDate = DateTime.Now,
        Items = new List<InvoiceItem>
        {
            new InvoiceItem { Name = "Product A", Quantity = 2, Price = 50 },
            new InvoiceItem { Name = "Product B", Quantity = 1, Price = 75 }
        }
    };

    return View(model);
}
```

---

## 📋 **Key Points:**

- ✅ **Layout is preserved**.
- ✅ **Model binding works** as usual.
- ✅ **No third-party libraries** — **only Microsoft and browser-native features**.
- ✅ The browser’s **Print → Save as PDF** is triggered on button click.

---

### ⚡ **If You Want Server-Side PDF (C# Only)**  
If **browser printing** isn’t sufficient and you want a **true PDF file generation** via C#, you'd need to explore **Microsoft SSRS (SQL Server Reporting Services)** or **Power BI Embedded**, which are official Microsoft tools.  

Would you like help implementing the **SSRS** approach, or does the client-side solution meet your needs? 🚀
--------------------------------------------------------------------------------------------------------------------------------
