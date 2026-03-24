**29.** Recipe Sharing: Recipes, Ingredients, Users. A recipe can have multiple ingredients. An ingredient can be used in multiple recipes. A user can save multiple recipes.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| username | VARCHAR(50) | NOT NULL, UNIQUE |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### recipes
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| author_id | BIGINT | FK → users(id), NOT NULL |
| title | VARCHAR(200) | NOT NULL |
| description | TEXT | |
| prep_time_min | INT | |
| cook_time_min | INT | |
| servings | INT | |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### ingredients
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL, UNIQUE |
| category | VARCHAR(50) | (DAIRY, MEAT, VEGETABLE, SPICE) |

### recipe_ingredients (M:N — recipes ↔ ingredients)
| Column | Type | Constraints |
|--------|------|-------------|
| recipe_id | BIGINT | FK → recipes(id), NOT NULL |
| ingredient_id | BIGINT | FK → ingredients(id), NOT NULL |
| quantity | DECIMAL(8,2) | NOT NULL |
| unit | VARCHAR(20) | NOT NULL (g, ml, cup, tbsp, piece) |
| PK(recipe_id, ingredient_id) | | |

### saved_recipes (M:N — users ↔ recipes)
| Column | Type | Constraints |
|--------|------|-------------|
| user_id | BIGINT | FK → users(id), NOT NULL |
| recipe_id | BIGINT | FK → recipes(id), NOT NULL |
| saved_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| PK(user_id, recipe_id) | | |

## Relationships
- users → recipes: **1:N** (author)
- recipes ↔ ingredients: **M:N** via `recipe_ingredients`
- users ↔ recipes: **M:N** via `saved_recipes` (bookmarks)

## ER Diagram (Text)
```
[users] 1───N [recipes] 1───M [recipe_ingredients] M───1 [ingredients]
   │
  M:N
   │
[saved_recipes]
   │
  M:1
   │
[recipes]
```

## Example & Insight
The `quantity` + `unit` columns on the junction table are critical — "2 cups flour" vs "1 tsp salt" live on the relationship, not on the ingredient itself. To find "recipes I can make with what I have," query: `SELECT r.* FROM recipes r WHERE NOT EXISTS (SELECT 1 FROM recipe_ingredients ri WHERE ri.recipe_id = r.id AND ri.ingredient_id NOT IN (:myIngredientIds))`.

## Trade-off
Normalizing ingredients avoids duplicates ("Tomato" vs "tomato" vs "Tomatoes") but requires discipline or a dedup process. Alternatively, store ingredient text as free-form on the junction table — simpler, but you lose the ability to search "all recipes with garlic."
