# NutriScan - Product Specification

## Product Overview

NutriScan is a free, ad-free nutrition tracking app that allows users to photograph nutrition labels from food products, extract nutritional information using AI-powered OCR, and track their daily intake through custom meal planning. The app supports tracking any nutritional component (calories, sodium, saturated fats, sugars, etc.) without being tied to a specific diet or health goal.

## User Stories

1. As a user, I want to take or upload a photo of a nutrition label so that I can extract the nutritional information from it.
2. As a user, I want the app to automatically extract nutrition data (calories, fats, sugars, sodium, etc.) from the photo using AI OCR.
3. As a user, I want to save extracted products to my personal database for future reference.
4. As a user, I want to create custom meal plans by adding products and specifying portion sizes in grams.
5. As a user, I want the app to calculate total nutrition for each meal based on the products and their portions.
6. As a user, I want to track my daily nutrition intake across all meals.
7. As a user, I want to track any nutritional component I choose (not limited to predefined categories).
8. As a user, I want the app to be free and without advertisements.

## Functional Requirements

### Photo Upload & OCR
- Users can capture photos using their device camera or upload existing images.
- The app sends the image to an AI OCR service (OpenAI-compatible endpoint) to extract nutrition information.
- The OCR extracts: product name, serving size, and all nutrition table values (energy, fats, carbohydrates, sugars, proteins, sodium, etc.).
- Users can review and edit extracted data before saving.

### Nutrition Tracking
- Extracted products are stored in a local product database.
- Users can search their saved products when creating meals.
- Users can manually add custom foods with nutrition info.

### Meal Planning
- Users create meals and add products with portion sizes (in grams).
- The app calculates nutrition totals for each meal based on the product's per-100g values and the portion size.
- Users can view daily totals across all meals.

### Custom Tracking
- Users can define custom nutritional components to track (e.g., "saturated fat", "fiber", "vitamin C").
- The app tracks any numeric nutrition value, not just predefined categories.

## Non-Functional Requirements

- **Free and ad-free**: The app must be completely free with no advertisements.
- **Privacy**: All data is stored locally in the browser (localStorage). No user data is sent to external servers except the OCR API call (which is user-configured).
- **Browser-based**: The app runs entirely in the browser with no build step required.
- **No external dependencies**: Uses only vanilla JavaScript, HTML, and CSS.

## Data Model

### Product
```javascript
{
  id: string,           // unique identifier
  name: string,         // product name
  servingSize: string,  // e.g., "30 g", "200 ml"
  servingGrams: number, // grams per serving
  nutritionPer100g: {   // nutrition values per 100g
    energyKj: number,
    energyKcal: number,
    fat: number,
    saturatedFat: number,
    carbohydrates: number,
    sugars: number,
    fiber: number,
    protein: number,
    sodium: number,
    [customField: string]: number  // for user-defined components
  },
  ingredients: string,  // ingredients list (optional)
  allergens: string[],  // allergen information (optional)
  language: string,     // language of the label (e.g., "nl", "de", "fr")
  createdAt: string     // ISO date string
}
```

### Meal
```javascript
{
  id: string,
  name: string,         // e.g., "Breakfast", "Lunch"
  date: string,         // ISO date string (YYYY-MM-DD)
  items: [{
    productId: string,  // reference to Product
    portionGrams: number,
    nutrition: {        // calculated nutrition for this portion
      energyKj: number,
      energyKcal: number,
      fat: number,
      // ... all nutrition fields
    }
  }],
  totals: {             // total nutrition for the meal
    energyKj: number,
    energyKcal: number,
    fat: number,
    // ... all nutrition fields
  }
}
```

### DailyLog
```javascript
{
  date: string,         // ISO date string (YYYY-MM-DD)
  meals: string[],      // array of meal IDs
  totals: {             // total nutrition for the day
    energyKj: number,
    energyKcal: number,
    fat: number,
    // ... all nutrition fields
  }
}
```

## UI Requirements

- Simple, clean interface with no clutter.
- Main screens:
  1. **Scan**: Camera/upload interface for nutrition labels.
  2. **Products**: List of saved products with search.
  3. **Meal Planner**: Create/edit meals, add products with portions.
  4. **Daily Log**: View daily nutrition totals.
- Responsive design for mobile and desktop.
- Language: Spanish (primary), with support for multi-language labels.

## Examples from Shared Images

### Example 1: Chocolate Bar (Image 1 - German/French/Italian)
- **Product**: Dr. Schär chocolate bar (gluten-free)
- **Serving size**: 30 g (1 Melto)
- **Nutrition per 100g**:
  - Energy: 2292 kJ / 549 kcal
  - Fat: 33 g
  - Saturated fat: 13 g
  - Carbohydrates: 55 g
  - Sugars: 45 g
  - Fiber: 2.4 g
  - Protein: 6.8 g
  - Sodium: 0.18 g
- **Nutrition per serving (30g)**:
  - Energy: 688 kJ / 165 kcal
  - Fat: 10 g
  - Saturated fat: 3.9 g
  - Carbohydrates: 16 g
  - Sugars: 14 g
  - Fiber: 0.7 g
  - Protein: 2.0 g
  - Sodium: 0.05 g

### Example 2: Juice Bottle (Image 2 - Dutch)
- **Product**: Versgeperst appel-sinaasappel- en mangosap (Freshly squeezed apple-orange-mango juice)
- **Serving size**: 200 ml (glass)
- **Nutrition per 100 ml**:
  - Energy: 199 kJ / 47 kcal
  - Fat: 0 g
  - Saturated fat: 0 g
  - Unsaturated fat: 0 g
  - Carbohydrates: 11 g
  - Sugars: 10 g
  - Honey: 0.7 g
  - Protein: 0.4 g
  - Sodium: 0 g
- **Nutrition per glass (200 ml)**:
  - Energy: 399 kJ / 94 kcal
  - Carbohydrates: 22 g
  - Sugars: 20 g
  - Protein: 0.8 g

### Example 3: Olive Oil Spray (Image 3 - Dutch)
- **Product**: Extra olijfolie van de eerste persing (Extra virgin olive oil spray)
- **Serving size**: 3 g (spray)
- **Nutrition per 100 ml**:
  - Energy: 3404 kJ / 828 kcal
  - Fat: 92 g
  - Saturated fat: 14 g
  - Carbohydrates: 0 g
  - Sugars: 0 g
  - Protein: 0 g
  - Sodium: 0 g
- **Vitamin E**: 150% of daily reference intake per 100 ml

## Technical Stack

- **Runtime**: Node 24 with ES modules
- **Testing**: Node's built-in test runner (`node --test`)
- **Build**: No build step
- **UI**: Static web page in `public/` directory (vanilla HTML, CSS, JavaScript)
- **AI OCR**: OpenAI-compatible endpoint (user-configured via URL and API key)
- **Storage**: Browser localStorage for all user data

## Modules

### `src/ocr.js` - OCR Processing
- **`extractNutrition(imageData, apiKey, apiEndpoint)`**: Sends an image to the AI OCR service and parses the response into a structured nutrition object.
  - **Parameters**:
    - `imageData`: Base64-encoded image string or Blob
    - `apiKey`: OpenAI-compatible API key (user-provided)
    - `apiEndpoint`: URL of the OpenAI-compatible endpoint
  - **Returns**: `Promise<NutritionExtraction>`
  - **Example**:
    ```javascript
    // Input: Base64 image of a nutrition label
    // Output: {
    //   productName: "Dr. Schär Melto",
    //   servingSize: "30 g",
    //   servingGrams: 30,
    //   nutritionPer100g: {
    //     energyKj: 2292,
    //     energyKcal: 549,
    //     fat: 33,
    //     saturatedFat: 13,
    //     carbohydrates: 55,
    //     sugars: 45,
    //     fiber: 2.4,
    //     protein: 6.8,
    //     sodium: 0.18
    //   }
    // }
    ```

### `src/nutrition.js` - Nutrition Calculations
- **`calculatePortionNutrition(product, portionGrams)`**: Calculates nutrition values for a given portion size.
  - **Parameters**:
    - `product`: Product object with `nutritionPer100g` and `servingGrams`
    - `portionGrams`: Number of grams in the portion
  - **Returns**: `Object` with nutrition values for the portion
  - **Example**:
    ```javascript
    // Input: product with nutritionPer100g.energyKcal = 549, portionGrams = 30
    // Output: { energyKcal: 164.7, ... }
    ```

- **`sumNutrition(nutritionArray)`**: Sums nutrition values across an array of nutrition objects.
  - **Parameters**: `nutritionArray`: Array of nutrition objects
  - **Returns**: `Object` with summed nutrition values
  - **Example**:
    ```javascript
    // Input: [{ energyKcal: 165 }, { energyKcal: 94 }]
    // Output: { energyKcal: 259 }
    ```

### `src/storage.js` - Local Storage Management
- **`saveProduct(product)`**: Saves a product to localStorage.
  - **Parameters**: `product`: Product object
  - **Returns**: `void`

- **`getProducts()`**: Retrieves all saved products.
  - **Parameters**: None
  - **Returns**: `Product[]`

- **`saveMeal(meal)`**: Saves a meal to localStorage.
  - **Parameters**: `meal`: Meal object
  - **Returns**: `void`

- **`getMealsByDate(date)`**: Retrieves all meals for a given date.
  - **Parameters**: `date`: ISO date string (YYYY-MM-DD)
  - **Returns**: `Meal[]`

- **`getDailyTotals(date)`**: Calculates total nutrition for a given date.
  - **Parameters**: `date`: ISO date string (YYYY-MM-DD)
  - **Returns**: `Object` with total nutrition values

### `src/mealPlanner.js` - Meal Planning Logic
- **`createMeal(name, date)`**: Creates a new meal.
  - **Parameters**:
    - `name`: Meal name (e.g., "Breakfast")
    - `date`: ISO date string
  - **Returns**: `Meal` object

- **`addProductToMeal(meal, productId, portionGrams)`**: Adds a product to a meal with a portion size.
  - **Parameters**:
    - `meal`: Meal object
    - `productId`: String ID of the product
    - `portionGrams`: Number of grams
  - **Returns**: `Meal` object with updated items and totals

- **`getMealTotals(meal)`**: Calculates total nutrition for a meal.
  - **Parameters**: `meal`: Meal object
  - **Returns**: `Object` with total nutrition values

## Acceptance Criteria

### AC-1: Photo Upload
- Users can upload images from their device or capture photos using the camera.
- The app accepts common image formats (JPEG, PNG).

### AC-2: OCR Extraction
- The app sends the uploaded image to the configured AI OCR endpoint.
- The OCR extracts nutrition information including energy, fats, carbohydrates, sugars, proteins, and sodium.
- Users can review and edit the extracted data before saving.

### AC-3: Product Storage
- Extracted products are saved to localStorage with all nutrition information.
- Users can view their saved products list.
- Users can search for saved products by name.

### AC-4: Meal Creation
- Users can create meals with a name and date.
- Users can add products to meals with portion sizes in grams.
- The app calculates nutrition totals for each meal.

### AC-5: Daily Tracking
- Users can view daily nutrition totals across all meals.
- The app sums nutrition values from all meals for a given date.

### AC-6: Custom Tracking
- Users can define custom nutritional components to track.
- The app supports tracking any numeric nutrition value, not just predefined categories.

### AC-7: Free and Ad-Free
- The app is completely free with no advertisements.
- No paywalls or premium features.

### AC-8: Privacy
- All user data is stored locally in the browser.
- No user data is sent to external servers except the OCR API call (which uses user-provided credentials).

### AC-9: Multi-Language Support
- The app can extract nutrition information from labels in multiple languages (Dutch, German, French, Italian, Spanish, etc.).
- The OCR handles multi-language nutrition tables as shown in the example images.

### AC-10: Nutrition Calculation Accuracy
- The app correctly calculates nutrition values for any portion size based on per-100g values.
- Example: A 30g portion of a product with 549 kcal per 100g should show 164.7 kcal.