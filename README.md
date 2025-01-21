# sent1
using System;
using System.Collections.Generic;
using System.Windows.Forms;

namespace WindowsFormsApp3
{
    // Class to represent a menu item
    public class MenuItem
    {
        public string Name { get; set; }
        public int Price { get; set; }
        public int Quantity { get; set; }
        public bool IsSelected { get; set; }
    }

    public partial class Form1 : Form
    {
        // List to store all menu items
        private List<MenuItem> menuItems;

        public Form1()
        {
            InitializeComponent();
            InitializeMenuItems();
        }

        // Initialize menu items
        private void InitializeMenuItems()
        {
            menuItems = new List<MenuItem>
            {
                new MenuItem { Name = "Coffee", Price = 75, Quantity = 0 },
                new MenuItem { Name = "Green Tea", Price = 50, Quantity = 0 },
                new MenuItem { Name = "Noodle", Price = 100, Quantity = 0 },
                new MenuItem { Name = "Pizza", Price = 200, Quantity = 0 }
            };

            // Update UI
            tbCoffeePrice.Text = menuItems[0].Price.ToString();
            tbCoffeeQuantity.Text = menuItems[0].Quantity.ToString();

            tbGreenTeaPrice.Text = menuItems[1].Price.ToString();
            tbGreenTeaQuantity.Text = menuItems[1].Quantity.ToString();

            tbNoodlePrice.Text = menuItems[2].Price.ToString();
            tbNoodleQuantity.Text = menuItems[2].Quantity.ToString();

            tbPizzaPrice.Text = menuItems[3].Price.ToString();
            tbPizzaQuantity.Text = menuItems[3].Quantity.ToString();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            try
            {
                // Update selected state and quantities from UI
                UpdateMenuItemsFromUI();

                // Calculate total price
                int total = CalculateTotal();

                // Apply discounts
                total = ApplyDiscounts(total);

                // Display total
                tbTotal.Text = total.ToString();

                // Handle cash input and calculate change
                HandlePayment(total);
            }
            catch (Exception ex)
            {
                MessageBox.Show("An error occurred: " + ex.Message, "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }

        private void UpdateMenuItemsFromUI()
        {
            menuItems[0].IsSelected = chbCoffee.Checked;
            menuItems[0].Quantity = chbCoffee.Checked ? GetIntFromTextBox(tbCoffeeQuantity) : 0;

            menuItems[1].IsSelected = chbGreenTea.Checked;
            menuItems[1].Quantity = chbGreenTea.Checked ? GetIntFromTextBox(tbGreenTeaQuantity) : 0;

            menuItems[2].IsSelected = chbNoodle.Checked;
            menuItems[2].Quantity = chbNoodle.Checked ? GetIntFromTextBox(tbNoodleQuantity) : 0;

            menuItems[3].IsSelected = chbPizza.Checked;
            menuItems[3].Quantity = chbPizza.Checked ? GetIntFromTextBox(tbPizzaQuantity) : 0;
        }

        private int CalculateTotal()
        {
            int total = 0;
            foreach (var item in menuItems)
            {
                if (item.IsSelected)
                {
                    total += item.Price * item.Quantity;
                }
            }
            return total;
        }

        private int ApplyDiscounts(int total)
        {
            if (chbDiscountAll.Checked)
            {
                int discountAll = GetIntFromTextBox(tbDiscountAll);
                total -= (total * discountAll) / 100;
            }
            else
            {
                int beverageTotal = 0;
                int foodTotal = 0;

                foreach (var item in menuItems)
                {
                    if (item.Name == "Coffee" || item.Name == "Green Tea")
                    {
                        beverageTotal += item.Price * item.Quantity;
                    }
                    else
                    {
                        foodTotal += item.Price * item.Quantity;
                    }
                }

                if (chbDiscountBeverage.Checked)
                {
                    int discountBeverage = GetIntFromTextBox(tbDiscountBeverage);
                    total -= (beverageTotal * discountBeverage) / 100;
                }

                if (chbDiscountFood.Checked)
                {
                    int discountFood = GetIntFromTextBox(tbDiscountFood);
                    total -= (foodTotal * discountFood) / 100;
                }
            }

            return total;
        }

        private void HandlePayment(int total)
        {
            int cash = GetIntFromTextBox(tbCash);

            if (cash < total)
            {
                MessageBox.Show("Insufficient cash!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            int change = cash - total;
            tbChange.Text = change.ToString();

            // Calculate denominations
            CalculateDenominations(change);
        }

        private void CalculateDenominations(int change)
        {
            int[] denominations = { 1000, 500, 100, 50, 20, 10, 5, 1 };
            TextBox[] textBoxes = { tb1000, tb500, tb100, tb50, tb20, tb10, tb5, tb1 };

            for (int i = 0; i < denominations.Length; i++)
            {
                int count = change / denominations[i];
                change %= denominations[i];
                textBoxes[i].Text = count.ToString();
            }
        }

        private int GetIntFromTextBox(TextBox textBox)
        {
            int value = 0;
            if (!int.TryParse(textBox.Text, out value))
            {
                MessageBox.Show($"Invalid input in {textBox.Name}. Please enter a valid number.", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Warning);
            }
            return value;
        }
    }
}
