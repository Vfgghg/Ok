Here’s how you can build an **Add Employee** page in ASP.NET MVC with a dynamic **Add Education** feature using JavaScript (and optionally jQuery) without Entity Framework.

---

### **1. Overview of the Page**
- The employee form will include fields like name, age, and department.
- There will be a dynamic "Add Education" section where:
  - One row is mandatory.
  - Users can add or delete additional education rows dynamically.

---

### **2. Stored Procedure**
The stored procedure should accept employee details and education as parameters. It’s assumed the SP will be something like:

```sql
CREATE PROCEDURE SP_AddEmployee
    @EmployeeName NVARCHAR(100),
    @Age INT,
    @Department NVARCHAR(100),
    @EducationDetails NVARCHAR(MAX) -- Pass education details as JSON or CSV
AS
BEGIN
    -- Example insert into Employee table
    INSERT INTO Employees (Name, Age, Department)
    VALUES (@EmployeeName, @Age, @Department);

    -- Fetch the last inserted EmployeeID
    DECLARE @EmployeeID INT = SCOPE_IDENTITY();

    -- Parse and insert education details
    -- (Assume JSON or CSV is parsed in SP or externally before being inserted into an Education table)
    -- Example:
    -- INSERT INTO Education (EmployeeID, Degree, Institution)
END
```

You can modify this according to your setup.

---

### **3. View**

#### `AddEmployee.cshtml`
This view includes a form for employee details and a dynamic education section using JavaScript.

```html
@{
    ViewBag.Title = "Add Employee";
}

<h2>Add Employee</h2>

<form id="addEmployeeForm">
    <div class="mb-3">
        <label for="employeeName" class="form-label">Employee Name</label>
        <input type="text" id="employeeName" name="employeeName" class="form-control" required />
    </div>

    <div class="mb-3">
        <label for="age" class="form-label">Age</label>
        <input type="number" id="age" name="age" class="form-control" required />
    </div>

    <div class="mb-3">
        <label for="department" class="form-label">Department</label>
        <input type="text" id="department" name="department" class="form-control" required />
    </div>

    <h4>Education</h4>
    <div id="educationContainer">
        <!-- Dynamic rows will go here -->
        <div class="row mb-2 education-row">
            <div class="col-md-5">
                <input type="text" name="degree[]" class="form-control" placeholder="Degree" required />
            </div>
            <div class="col-md-5">
                <input type="text" name="institution[]" class="form-control" placeholder="Institution" required />
            </div>
            <div class="col-md-2">
                <button type="button" class="btn btn-danger remove-education" disabled>Delete</button>
            </div>
        </div>
    </div>
    <button type="button" id="addEducation" class="btn btn-primary">Add Education</button>

    <div class="mt-4">
        <button type="submit" class="btn btn-success">Submit</button>
    </div>
</form>

<!-- Include External JS -->
<script src="~/js/addEmployee.js"></script>
```

---

### **4. External JavaScript File**

#### `wwwroot/js/addEmployee.js`

```javascript
$(document).ready(function () {
    // Add a new education row
    $("#addEducation").on("click", function () {
        var newRow = `
            <div class="row mb-2 education-row">
                <div class="col-md-5">
                    <input type="text" name="degree[]" class="form-control" placeholder="Degree" required />
                </div>
                <div class="col-md-5">
                    <input type="text" name="institution[]" class="form-control" placeholder="Institution" required />
                </div>
                <div class="col-md-2">
                    <button type="button" class="btn btn-danger remove-education">Delete</button>
                </div>
            </div>`;
        $("#educationContainer").append(newRow);
    });

    // Remove an education row
    $(document).on("click", ".remove-education", function () {
        if ($(".education-row").length > 1) {
            $(this).closest(".education-row").remove();
        } else {
            alert("At least one education row is mandatory.");
        }
    });

    // Submit form via AJAX
    $("#addEmployeeForm").on("submit", function (e) {
        e.preventDefault();

        // Gather data
        var employeeData = {
            employeeName: $("#employeeName").val(),
            age: $("#age").val(),
            department: $("#department").val(),
            educationDetails: []
        };

        // Gather education details
        $(".education-row").each(function () {
            var degree = $(this).find("input[name='degree[]']").val();
            var institution = $(this).find("input[name='institution[]']").val();
            employeeData.educationDetails.push({ degree: degree, institution: institution });
        });

        // Send data via AJAX
        $.ajax({
            url: '/Employee/AddEmployee', // Update to match your controller and action
            type: 'POST',
            contentType: 'application/json',
            data: JSON.stringify(employeeData),
            success: function (response) {
                if (response.success) {
                    alert(response.message || "Employee added successfully!");
                    window.location.reload(); // Reload or redirect
                } else {
                    alert(response.message || "Error adding employee.");
                }
            },
            error: function (xhr, status, error) {
                console.error("Error:", error);
                alert("An unexpected error occurred.");
            }
        });
    });
});
```

---

### **5. Controller**

#### `EmployeeController.cs`

```csharp
[HttpPost]
public JsonResult AddEmployee([FromBody] EmployeeRequestModel employee)
{
    try
    {
        // Call your custom class to handle database operations
        clsmasteradd addOperation = new clsmasteradd();

        // Convert education details to JSON or a string format as needed by the SP
        string educationDetails = Newtonsoft.Json.JsonConvert.SerializeObject(employee.EducationDetails);

        // Call the method to execute SP
        int result = addOperation.ExecuteAdd(
            employee.EmployeeName,
            employee.Age,
            employee.Department,
            educationDetails
        );

        if (result == 1) // Assuming 1 means success
        {
            return Json(new { success = true, message = "Employee added successfully." });
        }
        else
        {
            return Json(new { success = false, message = "Failed to add employee." });
        }
    }
    catch (Exception ex)
    {
        return Json(new { success = false, message = "Error: " + ex.Message });
    }
}
```

---

### **6. Request Model**

#### `EmployeeRequestModel.cs`

```csharp
public class EmployeeRequestModel
{
    public string EmployeeName { get; set; }
    public int Age { get; set; }
    public string Department { get; set; }
    public List<EducationDetail> EducationDetails { get; set; }
}

public class EducationDetail
{
    public string Degree { get; set; }
    public string Institution { get; set; }
}
```

---

### **7. Database Handler**

#### `clsmasteradd.cs`

```csharp
using System.Data;
using System.Data.SqlClient;
using System.Configuration;

public class clsmasteradd
{
    private string _connectionString;

    public clsmasteradd()
    {
        _connectionString = ConfigurationManager.ConnectionStrings["DefaultConnection"].ConnectionString;
    }

    public int ExecuteAdd(string employeeName, int age, string department, string educationDetails)
    {
        using (SqlConnection conn = new SqlConnection(_connectionString))
        {
            SqlCommand cmd = new SqlCommand("SP_AddEmployee", conn);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.AddWithValue("@EmployeeName", employeeName);
            cmd.Parameters.AddWithValue("@Age", age);
            cmd.Parameters.AddWithValue("@Department", department);
            cmd.Parameters.AddWithValue("@EducationDetails", educationDetails);

            conn.Open();
            return cmd.ExecuteNonQuery(); // Assuming SP returns rows affected or 1 for success
        }
    }
}
```

---

### **Benefits of This Approach**
- Dynamic addition/removal of education rows.
- Clean and modular code with separation of concerns.
- Reusable JavaScript and server-side logic.

Let me know if you need further clarifications!
