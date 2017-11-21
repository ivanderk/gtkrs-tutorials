# Maintaining External State

In this chapter, we are going to have some state that we will manipulate with the UI. We
therefore need a means of storing and loading values from that state. The program that
we are going to create has a single component: a health value.

As it turns out, we can take advantage of atomic primitives directly, such as
**AtomicUsize**, to store this value for sharing across multiple immutable closures. This
atomic value can be manipulated without requiring mutable access to the inner value. So this
can be passed around through an immutable borrow, and modified while being immutably borrowed
at multiple locations.

```rust
pub struct HealthComponent(AtomicUsize);
```

While we are at it, we can go ahead and abstract some logic to this component by implementing
some useful methods for initializing the health, subtracting health, and healing health.

```rust
impl HealthComponent {
    fn new(initial: usize) -> HealthComponent { HealthComponent(AtomicUsize::new(initial)) }

    fn get_health(&self) -> usize { self.0.load(Ordering::SeqCst) }

    fn subtract(&self, value: usize) -> usize {
        let current = self.0.load(Ordering::SeqCst);
        let new = if current < value { 0 } else { current - value };
        self.0.store(new, Ordering::SeqCst);
        new
    }

    fn heal(&self, value: usize) -> usize {
        let original = self.0.fetch_add(value, Ordering::SeqCst);
        original + value
    }
}
```
