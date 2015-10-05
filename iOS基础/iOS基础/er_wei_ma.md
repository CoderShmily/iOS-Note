# 二维码

```swift
 
import UIKit
import AVFoundation // 导入框架

class QRCodeViewController: UIViewController, UITabBarDelegate {
    
    @IBOutlet weak var scanlineView: UIImageView!
    
    @IBOutlet weak var containerHeightCons: NSLayoutConstraint!
    @IBOutlet weak var scanlineTopCons: NSLayoutConstraint!
    
    /// 监听关闭按钮点击
    @IBAction func closeItemClick(sender: AnyObject) {
        dismissViewControllerAnimated(true, completion: nil)
    }

    /// 自定义工具条
    @IBOutlet weak var customTabBar: UITabBar!
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // 1.设置tabbar
        customTabBar.selectedItem = customTabBar.items![0]
        customTabBar.delegate = self
    }
    
    override func viewDidAppear(animated: Bool) {
        super.viewDidAppear(animated)
        // 1.执行扫描动画
        startAnimation()
        
        // 2.开始扫描二维码
        startScanQRCode()
    }
    
    private func startScanQRCode()
    {
        // 1.判断是否可以添加输入设备
        if !session.canAddInput(inputDevice)
        {
            return
        }
        // 2.判断是否可以添加输出对象
        if !session.canAddOutput(output)
        {
            return
        }
        // 3.添加输入和输出到会话中
        session.addInput(inputDevice)
        session.addOutput(output)
        // 4.设置输出解析数据类型
        // 必须在输出对象添加到会话之后才可以设置, 否则会报错
        output.metadataObjectTypes = output.availableMetadataObjectTypes
        // 5.设置输出代理, 监听解析到得结果
        output.setMetadataObjectsDelegate(self, queue: dispatch_get_main_queue())
        
        // 6.添加预览图层
        view.layer.insertSublayer(previewLayer, atIndex: 0)
        
        // 6.利用会话开始扫描
        session.startRunning()
    }
    
    private func startAnimation()
    {
        // 1.将冲击波移动的底部和容器顶部对齐
        scanlineTopCons.constant = -containerHeightCons.constant
        view.layoutIfNeeded()
        
        // 2.执行动画
        UIView.animateWithDuration(2.0) { [unowned self] () -> Void in
            // 告诉系统UIView执行无数次
            UIView.setAnimationRepeatCount(MAXFLOAT)
            self.scanlineTopCons.constant = self.containerHeightCons.constant
            self.view.layoutIfNeeded()
        }

    }
    
    // MARK: - UITabBarDelegate
    func tabBar(tabBar: UITabBar, didSelectItem item: UITabBarItem) {
//        print(item.tag)
        // 1.修改容器的高度
        containerHeightCons.constant = (item.tag == 1) ? 150 : 300
        
        // 2.停止动画
        // UIView动画的本质就是图层动画
        scanlineView.layer.removeAllAnimations()
        
        // 3.重新开始动画
        startAnimation()
        
    }
    
    // MARK: - 懒加载
    /// 1.创建输入
    private lazy var inputDevice: AVCaptureDeviceInput? =
    {
       let device = AVCaptureDevice.defaultDeviceWithMediaType(AVMediaTypeVideo)
        do{
            let input = try AVCaptureDeviceInput(device: device)
            return input
        }catch
        {
            return nil
        }
    }()
    /// 2.创建输出
    private lazy var output: AVCaptureMetadataOutput = {
       let output = AVCaptureMetadataOutput()
        return output
    }()
    /// 3.创建会话
    private lazy var session: AVCaptureSession = {
        let s = AVCaptureSession()
        s.sessionPreset = "AVCaptureSessionPreset1920x1080"
        return s
    }()
    /// 4.创建预览图层
    private lazy var previewLayer: AVCaptureVideoPreviewLayer = {
       let layer = AVCaptureVideoPreviewLayer(session: self.session)
        layer.frame = UIScreen.mainScreen().bounds
        return layer
    }()
}

extension QRCodeViewController: AVCaptureMetadataOutputObjectsDelegate

{
    func captureOutput(captureOutput: AVCaptureOutput!, didOutputMetadataObjects metadataObjects: [AnyObject]!, fromConnection connection: AVCaptureConnection!)
    {
        NJLog(metadataObjects.last?.stringValue)
    }
}

```