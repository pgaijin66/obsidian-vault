
```
// FileSystemNode : a component which can be a file or a directory
type FileSystemNode interface {
	GetSize() int
}

// File : a leaf node in the file system
type File struct {
	size int
}

// GetSize : returns size of the file
func (f *File) GetSize() int {
	return f.size
}

// Directory : a composite node in the file system
type Directory struct {
	children []FileSystemNode
}

// GetSize : returns total size of the directory (including all children)
func (d *Directory) GetSize() int {
	size := 0
	for _, child := range d.children {
		size += child.GetSize()
	}
	return size
}

// AddChild : adds a new child node to the directory
func (d *Directory) AddChild(child FileSystemNode) {
	d.children = append(d.children, child)
}
```