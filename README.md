# 🛒 EasyShop E-Commerce API

This is a Spring Boot-based backend API for **EasyShop**, an online e-commerce store. The API supports user authentication, category and product management, and bug fixes for core logic.

---

## 🚀 Features Implemented

### ✅ Authentication & Authorization (Pre-existing)

* **User Registration** – `POST /register`
* **User Login** – `POST /login`
* JWT-based authentication is required for admin-level access.

---

### ✅ Phase 1: Categories Management

CRUD operations for product categories with admin-level access restrictions.

#### Endpoints

| Method | Endpoint                    | Access     | Description              |
| ------ | --------------------------- | ---------- | ------------------------ |
| GET    | `/categories`               | Public     | List all categories      |
| GET    | `/categories/{id}`          | Public     | Get category by ID       |
| GET    | `/categories/{id}/products` | Public     | Get products in category |
| POST   | `/categories`               | Admin only | Create a category        |
| PUT    | `/categories/{id}`          | Admin only | Update a category        |
| DELETE | `/categories/{id}`          | Admin only | Delete a category        |

* `@RestController` with `@RequestMapping("/categories")`
* Security controlled via `@PreAuthorize`
* `CategoriesController` uses injected DAOs to perform logic
* Supports `GET`, `POST`, `PUT`, `DELETE` for Category objects

#### Category controller methods

<img width="500" alt="Image" src="https://github.com/user-attachments/assets/6c5d3d0b-e5d2-41f2-a385-6fcdfde44fe8" />
<img width="500" alt="Image" src="https://github.com/user-attachments/assets/ef6370c6-8132-455c-93e5-24bb24fb23a2" />
<img width="500" alt="Image" src="https://github.com/user-attachments/assets/39df4458-dcb2-4596-985e-776f54996abd" />
<img width="500" alt="Image" src="https://github.com/user-attachments/assets/77381960-26a2-4137-835a-1e981b9cc07d" />
<img width="500" alt="Image" src="https://github.com/user-attachments/assets/ab113362-10cd-40f6-99a7-9b93fe29c2d2" />
<img width="500" alt="Image" src="https://github.com/user-attachments/assets/2977b533-0be4-4877-a6b5-a1b61c6ed8b7" />


#### CategoryDao methods

<img width="500" alt="Image" src="https://github.com/user-attachments/assets/34b0ab48-e0d5-4e82-8fa8-f056639d36a2" />

<img width="500" alt="Image" src="https://github.com/user-attachments/assets/c3f7b042-2bee-4c8f-ae7c-c9d8fb8ac4c9" />

<img width="500" alt="Image" src="https://github.com/user-attachments/assets/34b75cf9-ca73-4f41-832b-bc251b71a333" />

<img width="500" alt="Image" src="https://github.com/user-attachments/assets/4a6b6a5f-d4ca-4a54-b8ea-f4009ecb780f" />

<img width="500" alt="Image" src="https://github.com/user-attachments/assets/15e0b6ef-5280-434d-bb56-90f7939696e0" />

<img width="500" alt="Image" src="https://github.com/user-attachments/assets/db18dc70-c22f-4eb0-b509-0707e8186479" />


---

### ✅ Phase 2: Product Bug Fixes

#### 🔍 Bug Fix 1: Product Search

* Search parameters include:

  * `cat` (categoryId)
  * `minPrice`, `maxPrice`
  * `color`
    
* The Product search query was missing an additional price column for max price

```
String sql = "SELECT * FROM products " +
                "WHERE (category_id = ? OR ? = -1) " +
                "   AND (price >= ? OR ? = -1) " +
                "   AND (price <= ? OR ? = -1) " +
                "   AND (color = ? OR ? = '') ";
```
```
        categoryId = categoryId == null ? -1 : categoryId;
        minPrice = minPrice == null ? new BigDecimal("-1") : minPrice;
        maxPrice = maxPrice == null ? new BigDecimal("-1") : maxPrice;
        color = color == null ? "" : color;
```


#### 🔁 Bug Fix 2: Duplicate Products on Update

* Logic corrected in ProductsController class to use the update method instead the create method

```
    @PutMapping("{id}")
    @PreAuthorize("hasRole('ROLE_ADMIN')")
    public void updateProduct(@PathVariable int id, @RequestBody Product product)
    {
        try
        {
            productDao.update(id, product);
        }
        catch(Exception ex)
        {
            throw new ResponseStatusException(HttpStatus.INTERNAL_SERVER_ERROR, "Oops... our bad.");
        }
    }
```

---

## 🧠 Code Highlight

### MySqlCategoryDao

* I find this the most intersting piece of code because it retrieves a specific row based on the id. A very foundational method that is very practical and efficient.


```java
    @Override
    public Category getById(int categoryId)
    {
        try (Connection connection = getConnection())
        {
            String sql = "SELECT * FROM Categories WHERE category_id = ?";

            PreparedStatement statement = connection.prepareStatement(sql);
            statement.setInt(1, categoryId);

            ResultSet row = statement.executeQuery();

            if (row.next())
            {
                return mapRow(row);
            }
        }
        catch (SQLException e)
        {
            throw new RuntimeException(e);
        }
        return null;
    }
```

---

## 🧪 Technologies Used

* Java 17
* Spring Boot
* Spring Security
* MySQL
* Postman
* JWT Authentication

## 👤 Author

Created by **\[James Bosley]** as part of the Pluralsight Java Capstone Project.

