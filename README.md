#include <stdio.h>
#include <string.h>

#define MAX_PRODUCTS 5
#define MAX_CART 20

typedef struct
{
    int id;
    char name[30];
    float price;
    int quantity;
} Product;

Product products[MAX_PRODUCTS] = {
    {1, "T-Shirt", 599.0, 0},
    {2, "Jeans", 1299.0, 0},
    {3, "Shoes", 1999.0, 0},
    {4, "Watch", 1499.0, 0},
    {5, "Backpack", 899.0, 0}
};

Product cart[MAX_CART];
int cartCount = 0;

void showProducts()
{
    int i;

    printf("\n--- PRODUCT LIST ---\n");
    printf("ID\tProduct\t\tPrice\n");

    for (i = 0; i < MAX_PRODUCTS; i++)
    {
        printf("%d\t%-15s\tRs. %.2f\n",
               products[i].id,
               products[i].name,
               products[i].price);
    }
}

int findProduct(int id)
{
    int i;

    for (i = 0; i < MAX_PRODUCTS; i++)
    {
        if (products[i].id == id)
            return i;
    }

    return -1;
}

void addToCart()
{
    int id, qty, p, i;

    showProducts();

    printf("\nEnter Product ID: ");
    scanf("%d", &id);

    p = findProduct(id);

    if (p == -1)
    {
        printf("Invalid Product ID!\n");
        return;
    }

    printf("Enter Quantity: ");
    scanf("%d", &qty);

    if (qty <= 0)
    {
        printf("Invalid Quantity!\n");
        return;
    }

    for (i = 0; i < cartCount; i++)
    {
        if (cart[i].id == id)
        {
            cart[i].quantity += qty;
            printf("Cart Updated Successfully!\n");
            return;
        }
    }

    cart[cartCount] = products[p];
    cart[cartCount].quantity = qty;

    cartCount++;

    printf("Product Added to Cart!\n");
}

float viewCart()
{
    int i;
    float total = 0;
    float itemTotal;

    if (cartCount == 0)
    {
        printf("\nCart is Empty!\n");
        return 0;
    }

    printf("\n--- SHOPPING CART ---\n");

    for (i = 0; i < cartCount; i++)
    {
        itemTotal = cart[i].price * cart[i].quantity;
        total += itemTotal;

        printf("%s x %d = Rs. %.2f\n",
               cart[i].name,
               cart[i].quantity,
               itemTotal);
    }

    printf("\nGrand Total = Rs. %.2f\n", total);

    return total;
}

void removeItem()
{
    int id, i, j;

    if (cartCount == 0)
    {
        printf("\nCart is Empty!\n");
        return;
    }

    printf("Enter Product ID to Remove: ");
    scanf("%d", &id);

    for (i = 0; i < cartCount; i++)
    {
        if (cart[i].id == id)
        {
            for (j = i; j < cartCount - 1; j++)
            {
                cart[j] = cart[j + 1];
            }

            cartCount--;

            printf("Product Removed Successfully!\n");
            return;
        }
    }

    printf("Product Not Found in Cart!\n");
}

void checkout()
{
    int i;
    float total = 0;
    float itemTotal;

    FILE *fp;

    if (cartCount == 0)
    {
        printf("\nCart is Empty!\n");
        return;
    }

    printf("\n===== FINAL BILL =====\n");

    fp = fopen("bill.txt", "w");

    for (i = 0; i < cartCount; i++)
    {
        itemTotal = cart[i].price * cart[i].quantity;
        total += itemTotal;

        printf("%s x %d = Rs. %.2f\n",
               cart[i].name,
               cart[i].quantity,
               itemTotal);

        if (fp != NULL)
        {
            fprintf(fp, "%s x %d = Rs. %.2f\n",
                    cart[i].name,
                    cart[i].quantity,
                    itemTotal);
        }
    }

    printf("\nTotal Amount = Rs. %.2f\n", total);

    if (fp != NULL)
    {
        fprintf(fp, "\nTotal Amount = Rs. %.2f\n", total);
        fclose(fp);
    }

    printf("\nThank You for Shopping!\n");

    cartCount = 0;
}

int main()
{
    int choice;

    do
    {
        printf("\n===== ONLINE SHOPPING CART =====\n");
        printf("1. View Products\n");
        printf("2. Add to Cart\n");
        printf("3. View Cart\n");
        printf("4. Remove Item\n");
        printf("5. Checkout\n");
        printf("6. Exit\n");

        printf("Enter Your Choice: ");
        scanf("%d", &choice);

        switch (choice)
        {
            case 1:
                showProducts();
                break;

            case 2:
                addToCart();
                break;

            case 3:
                viewCart();
                break;

            case 4:
                removeItem();
                break;

            case 5:
                checkout();
                break;

            case 6:
                printf("\nProgram Closed.\n");
                break;

            default:
                printf("\nInvalid Choice!\n");
        }

    } while (choice != 6);

    return 0;
}
