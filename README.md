

> 此教程适应于以webpack，vue\-cli，vite等脚手架构建的vue项目。当然，vue2和vue3都是可以滴。


## 1\. 前提：你的代码库已经提交到Github上


如果没有的话，请到GitHub上新建仓库，并把你本地的项目提交到GitHub上新建的仓库里。
具体方法，可以参考我的博客 [Git使用记录 \- 持续更新 \- 将本地项目关联到远程仓库](https://github.com)


## 2\. 在GitHub上设置部署配置


![image](https://img2024.cnblogs.com/blog/1086129/202409/1086129-20240912232641372-1175634403.png)


## 3\. 到你的项目根目录创建工作流文件


根目录下新建 `.github` 文件夹，然后在其目录下新建 `workflows` 文件夹，在里面新建 `main.yml` 。
![image](https://img2024.cnblogs.com/blog/1086129/202409/1086129-20240912232648554-750175118.png)


`main.yml` 里的内容如下：



```


|  | # 将静态内容部署到 GitHub Pages 的简易工作流程 |
| --- | --- |
|  | name: Deploy static content to Pages |
|  |  |
|  | on: |
|  | # 仅在推送到默认分支时运行。 |
|  | push: |
|  | branches: ['main'] |
|  |  |
|  | # 这个选项可以使你手动在 Action tab 页面触发工作流 |
|  | workflow_dispatch: |
|  |  |
|  | # 设置 GITHUB_TOKEN 的权限，以允许部署到 GitHub Pages。 |
|  | permissions: |
|  | contents: read |
|  | pages: write |
|  | id-token: write |
|  |  |
|  | # 允许一个并发的部署 |
|  | concurrency: |
|  | group: 'pages' |
|  | cancel-in-progress: true |
|  |  |
|  | jobs: |
|  | # 单次部署的工作描述 |
|  | deploy: |
|  | environment: |
|  | name: github-pages |
|  | url: ${{ steps.deployment.outputs.page_url }} |
|  | runs-on: ubuntu-latest |
|  | steps: |
|  | - name: Checkout |
|  | uses: actions/checkout@v3 |
|  | - name: Set up Node |
|  | uses: actions/setup-node@v3 |
|  | with: |
|  | node-version: 20 |
|  | cache: 'npm' |
|  | - name: Install dependencies |
|  | run: npm install |
|  | - name: Build |
|  | run: npm run build |
|  | - name: Setup Pages |
|  | uses: actions/configure-pages@v3 |
|  | - name: Upload artifact |
|  | uses: actions/upload-pages-artifact@v1 |
|  | with: |
|  | # Upload dist repository |
|  | path: './dist' |
|  | - name: Deploy to GitHub Pages |
|  | id: deployment |
|  | uses: actions/deploy-pages@v1 |


```

其中我们需要修改的内容：


1. node版本，根据你的项目实际使用版本设置
2. 打包目录，一般都是 `dist`，如果不是的话请修改
3. 项目脚本，此项目是使用npm构建，如果你使用的是pnpm，或者ymal等，需要手动修改。下面给出pnpm的设置：



```


|  | steps: |
| --- | --- |
|  | - name: Checkout |
|  | uses: actions/checkout@v4 |
|  | - name: Set up pnpm |
|  | uses: pnpm/action-setup@v4 |
|  | with: |
|  | version: 9 |
|  | - name: Set up Node |
|  | uses: actions/setup-node@v3 |
|  | with: |
|  | node-version: 20 |
|  | cache: 'pnpm' |
|  | - name: Install dependencies |
|  | run: pnpm install |
|  | - name: Build |
|  | run: pnpm run build |


```

pnpm的版本也根据你的实际使用而定，后面的内容都一样。其他的请自行查找。


## 4\. 修改你的项目部署根目录


正常情况下一般都在 `vite.config.ts` 或 `vue.config.js` 或 `webpack.config.js` 里，取决于你使用了哪种脚手架。
以 `vite.config.ts` 为例，存在 `base` 字段中。参考代码 [vite\-vue3\-charts](https://github.com)
![image](https://img2024.cnblogs.com/blog/1086129/202409/1086129-20240912232702188-1838110520.png)


如果你有封装的话，如上图，可能是一个变量，一般都在根目录的 `.env.production` 文件中，修改此变量的值即可。如下图：
![image](https://img2024.cnblogs.com/blog/1086129/202409/1086129-20240912232707295-1758561297.png)


## 5\. 提交代码，你的项目即可自动部署


或者到GitHub网站仓库目录，在 `Actions` 页签中，手动部署
![image](https://img2024.cnblogs.com/blog/1086129/202409/1086129-20240912232713225-1290668591.png)


## 6\. 访问路径


访问路径：\[github账号名称].github.io/\[仓库名称]
例如：[https://weizwz.github.io/vite\-vue3\-charts](https://github.com):[蓝猫机场](https://fenfang.org)


实例项目代码参考 [weiz\-vue3\-charts](https://github.com)


