import { Component, OnInit } from '@angular/core';
import { Item } from '../item.model';
import { ItemService } from '../item.service';

@Component({
  selector: 'app-items',
  templateUrl: './items.component.html',
  styleUrls: ['./items.component.css'],
})
export class ItemsComponent implements OnInit {
  items: Item[] = [];
  newItem: Item = { id: 0, name: '' };
  editingItem: Item | null = null;

  constructor(private itemService: ItemService) {}

  ngOnInit(): void {
    this.loadItems();
  }

  // Load items from API or localStorage
  loadItems(): void {
    this.itemService.getItems().subscribe((data) => {
      this.items = data;
      this.itemService.saveToLocal(this.items);
    });
  }

  // Add a new item
  addItem(): void {
    this.itemService.addItem(this.newItem).subscribe((item) => {
      this.items.push(item);
      this.newItem = { id: 0, name: '' }; // Reset form
      this.itemService.saveToLocal(this.items);
    });
  }

  // Edit an item
  editItem(item: Item): void {
    this.editingItem = { ...item };
  }

  // Save updated item
  updateItem(): void {
    if (this.editingItem) {
      this.itemService
        .updateItem(this.editingItem.id, this.editingItem)
        .subscribe((updatedItem) => {
          const index = this.items.findIndex((i) => i.id === updatedItem.id);
          this.items[index] = updatedItem;
          this.editingItem = null; // Close editing
          this.itemService.saveToLocal(this.items);
        });
    }
  }

  // Delete an item
  deleteItem(id: number): void {
    this.itemService.deleteItem(id).subscribe(() => {
      this.items = this.items.filter((item) => item.id !== id);
      this.itemService.saveToLocal(this.items);
    });
  }
}
