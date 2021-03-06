---
title: "How to contribute with the documentation"
linkTitle: "Beagle Docs"
weight: 1
description: >-
  Follow the steps required so your contribution can be accepted by Beagle developers team.
---

---

## Introdution to Hugo

{{% alert color="success" %}}

We use the tool [**Hugo**](https://gohugo.io/documentation/) to create static sites, which is ideal for documentation, and all the docs content is written in markdown.

If you have any difficulty with this syntax, you can access this [**basic guide**](https://www.markdownguide.org/basic-syntax/).

- It is important to remember that hugo has some shortcuts to create useful components. These shortcuts are very practical when creating a table, alert, glossary, embedded videos, etc. To learn more about using hugo shortcodes in markdown, visit this [**basic guide**]({{< ref path="/contribute/hugo-shortcodes" lang="en" >}}).
  {{% /alert %}}

## Requirements to contribute

All contributions are welcome, but to get starting contributing to Beagle it is good to have in mind these recommendations:

{{% alert color="warning" %}}

- Write clear and meaningful GIT messages.
- Follow the [**contribution guidelines**](https://github.com/ZupIT/beagle/blob/master/CONTRIBUTING.md) to make sure your pull requests will be considered.
- Make sure to include Github Special Keywords that reference the issue and automatically close it when the PR is dipped.
- When you make a small change in a PR, such as correcting a typo, any change in style or grammar, be sure to squash your commits so as not to get a large number of commits for a relatively small change.
- Include a good PR description that explains all the changes to the code, the reason for changing a code snippet and ensuring that there is enough information for the reviewer to understand your PR.
- Remember to use any of these [**templates**](https://github.com/ZupIT/docs-beagle/issues/new/choose) when you're opening an issue and also to mark it with the corresponding labels.
  {{% /alert %}}

## How to contribute
---
### **Editing a page**

- To edit content, simply navigate to the page to be edited using the left side menu or the search field. Then click on the "Edit this page" button, located on the right side menu.

- Once this is done, you will be redirected to the source file hosted on github in edit mode. Okay, now just edit the file. Then, open a pull request using the github website, filling out the form as shown in the image below:

![](/shared/contribute-pull-request.jpg)

### **Adding images**

- Images in this documentation are saved at a folder called shared at `/static/shared`. To add new images to the doc just copy them into this folder. From there, all images are accessible from any page or sub-page in the documentation.

- To load images on a page use the markdown interface for images (without adding a title) and list the address as `/shared/image-name` as follows:

```
![](/shared/beaglecomp.png)
```

{{% alert color = "success"%}}

The code above loads the image below:
![](/shared/beaglecomp.png)

 {{% /alert %}}

### **Creating a new page**

- To create a page, the process is similar to editing. Navigate to the location where you want to create a page (use the left side menu), in this case, instead of "Edit this page", click on "Create child page" located on the right side menu of the page.

### **Opening an issue**

- To help us identify problems in general, be it styling, reading, organizing or code architecture of the documentation, just open an issue using the "Create a documentation issue" button located on the right side menu of the page.

- Then you will be redirected to a github page with a label and a pre-established standard template for opening an issue, but feel free to edit the suggested template and propose the changes that make the most sense for your contribution.

## Fork our repository

- If you already have the basic knowledge of markdown, hugo, shortcodes and gitflow, feel free to fork our repository and suggest changes using your preferred text editor. If you have any questions, read our [**FAQ**]({{< ref path="/faq" lang="en" >}}), open an issue, or contact us via our [**email**](mailto:beagle@zup.com.br)
