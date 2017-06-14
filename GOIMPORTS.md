### 修改goimports导包优先级

###### goimports导包按照包的名字长短来排列包的优先级,实际开发中有个多个项目下都有某个库的代码,在自动导入包的时候可能导的是另一个项目的,可实际却要导入本项目中库,经过查看源代码,发现按照下面的步骤即可实现:<br>
###### 在golang.org/x/tools/imports/fix.go中找到sort.Sort(byImportPathShortLength(candidates))这一行注释掉,添加一下代码,然后重新编译goimports

```
var _firstDir string
_paths := strings.Split(filename, "/")
for i := range _paths {
	if _paths[i] == "src" {
		if len(_paths) > i+1 {
			_firstDir = _paths[i+1]
		}
		break
	}
}
//sort.Slice 在go1.8以下不可用,请参照byImportPathShortLength
sort.Slice(candidates, func(i, j int) bool {
	_paths = strings.Split(candidates[i].importPathShort, "/")
	fmt.Println(_paths, _firstDir)
	return _paths[0] == _firstDir
})

```
###### 由于上面的代码根据传入的文件名找到第一个目录,所以在goimports需要传入文件全名,我们用vim开发,VBudle管理插件,go插件是vim-go,相应的vim脚本在~/.vim/bundle/vim-go/autoload/go/fmt.vim,修改其中expand("%")为expand("%:p")即可。
