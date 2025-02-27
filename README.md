window.onload = function () {
    // URLs of the images
    var logoUrl = "https://cors-anywhere.herokuapp.com/https://freepngimg.com/download/adidas/58136-logo-brand-adidas-sports-free-transparent-image-hd.png";
    var stampUrl = "https://cors-anywhere.herokuapp.com/https://www.onlygfx.com/wp-content/uploads/2017/12/ok-stamp-2-1024x1024.png";

    // Fetch and load the images
    fetchImage(logoUrl, 'logoImage');
    fetchImage(stampUrl, 'stampImage');
};

// Fetch and display image, also convert it to Base64
function fetchImage(imageUrl, imgId) {
    fetch(imageUrl)
        .then(response => response.blob())  // Get the blob of the image
        .then(blob => {
            // Convert blob to Base64 string using FileReader
            var reader = new FileReader();
            reader.onloadend = function () {
                var imgElement = document.getElementById(imgId);
                imgElement.src = reader.result; // Set Base64 as the image source
            };
            reader.readAsDataURL(blob); // This converts the blob into a Base64-encoded string
        })
        .catch(error => {
            console.error("Error fetching image:", error);
        });
}

// Function to download the PDF
window.jsPDF = window.jspdf.jsPDF;
function download11() {
    var doc = new jsPDF({
        orientation: "p",
        unit: "mm",
        format: "a4"
    });

    var invoiceElement = document.getElementById("report11");

    // Use html2canvas to capture the entire invoice element as an image
    html2canvas(invoiceElement, {
        scale: 2,
        useCORS: true
    }).then(canvas => {
        var imgData = canvas.toDataURL("image/png");
        var imgWidth = 190; // Image width
        var pageHeight = 297; // Page height in mm (A4 size)
        var imgHeight = (canvas.height * imgWidth) / canvas.width;

        // Add the image to the PDF
        doc.addImage(imgData, "PNG", 10, 10, imgWidth, imgHeight);

        // Generate the PDF and open it in a new tab
        var pdfData = doc.output("blob");
        var pdfUrl = URL.createObjectURL(pdfData);
        window.open(pdfUrl, "_blank");
    }).catch(error => {
        console.error("Error generating PDF:", error);
    });
}

---------------------------------------------------------------------
window.onload = function () {
    // Get the URLs of images dynamically by id
    var logoUrl = document.getElementById("logoUrl").value;   // Get URL from hidden input or similar
    var stampUrl = document.getElementById("stampUrl").value; // Get URL from hidden input or similar

    // Fetch and convert the images to Base64
    fetchAndConvertToBase64(logoUrl, 'logoImage');
    fetchAndConvertToBase64(stampUrl, 'stampImage');
};

// Fetch the image by URL and convert it to Base64
function fetchAndConvertToBase64(imageUrl, imgId) {
    // CORS Proxy to bypass the CORS issue
    var proxyUrl = "https://cors-anywhere.herokuapp.com/";

    // Fetch the image using the proxy to avoid CORS issues
    fetch(proxyUrl + imageUrl)
        .then(response => response.blob()) // Get the image blob
        .then(blob => {
            // Create a FileReader to convert blob to Base64
            var reader = new FileReader();
            reader.onloadend = function () {
                var base64String = reader.result.split(',')[1]; // Get the Base64 string (remove the prefix)

                console.log("Base64 string: ", base64String); // Optionally log the Base64 string for inspection

                // Set the Base64 string as the source for the image
                var imgElement = document.getElementById(imgId);
                imgElement.src = reader.result; // Set the image source as Base64
            };

            // Read the image blob as Base64
            reader.readAsDataURL(blob);
        })
        .catch(error => {
            console.error("Error fetching image:", error);
        });
}
window.jsPDF = window.jspdf.jsPDF;
function download11() {
    var doc = new jsPDF();

    // Capture the content of the invoice
    html2canvas(document.getElementById("report11")).then(function (canvas) {
        // Convert canvas to image data URL
        var imgData = canvas.toDataURL('image/png');

        // Add the image to the PDF
        doc.addImage(imgData, 'PNG', 10, 10, 180, 160);

        // Create a Blob object from the PDF and open it in a new tab
        var pdfBlob = doc.output('blob');
        var pdfUrl = URL.createObjectURL(pdfBlob);

        // Open the PDF in a new tab
        window.open(pdfUrl, '_blank');
    });
}
